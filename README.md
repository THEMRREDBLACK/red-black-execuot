--[[ 
    RED BLACK EXECUTOR - VERSION 8.0
    Organized Dropdowns & Clean Editor
    Discord: https://discord.gg/ffuFGauPdS
]]

local Players = game:GetService("Players")
local HttpService = game:GetService("HttpService")
local RunService = game:GetService("RunService")
local Stats = game:GetService("Stats")
local LP = Players.LocalPlayer

-- 1. CONFIG STATE
local Config = {
    RGB_Enabled = false,
    Main_Color = Color3.fromRGB(255, 0, 0),
    Transparency = 0,
    Border_Thickness = 3,
    Show_Stats = false
}

-- 2. CORE GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "RedBlack_v8"
ScreenGui.Parent = LP:FindFirstChild("PlayerGui") or game:GetService("CoreGui")
ScreenGui.ResetOnSpawn = false

local Main = Instance.new("Frame")
Main.Size = UDim2.new(0, 750, 0, 480)
Main.Position = UDim2.new(0.5, -375, 0.5, -240)
Main.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
Main.BorderSizePixel = 0
Main.Active = true
Main.Draggable = true
Main.Parent = ScreenGui

local Corner = Instance.new("UICorner", Main)
local UIStroke = Instance.new("UIStroke", Main)
UIStroke.Thickness = Config.Border_Thickness
UIStroke.Color = Config.Main_Color

-- HEADER
local TopHeader = Instance.new("TextLabel", Main)
TopHeader.Size = UDim2.new(1, 0, 0, 45)
TopHeader.Position = UDim2.new(0, 0, 0, 5)
TopHeader.Text = "RED BLACK EXECUTOR"
TopHeader.TextColor3 = Config.Main_Color
TopHeader.Font = "GothamBlack"
TopHeader.TextSize = 24
TopHeader.BackgroundTransparency = 1

-- RGB LOOP
RunService.RenderStepped:Connect(function()
    local color = Config.RGB_Enabled and Color3.fromHSV(tick() % 5 / 5, 1, 1) or Config.Main_Color
    UIStroke.Color = color
    TopHeader.TextColor3 = color
end)

-- 3. NAVIGATION SIDEBAR
local Sidebar = Instance.new("Frame", Main)
Sidebar.Size = UDim2.new(0, 150, 1, -60)
Sidebar.Position = UDim2.new(0, 10, 0, 60)
Sidebar.BackgroundTransparency = 1

local function CreateNav(text, pos, callback)
    local btn = Instance.new("TextButton", Sidebar)
    btn.Size = UDim2.new(1, 0, 0, 40)
    btn.Position = UDim2.new(0, 0, 0, pos)
    btn.Text = text
    btn.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.Font = "GothamBold"
    Instance.new("UICorner", btn)
    btn.MouseButton1Click:Connect(callback)
end

-- 4. PAGES
local Container = Instance.new("Frame", Main)
Container.Size = UDim2.new(1, -180, 1, -70)
Container.Position = UDim2.new(0, 170, 0, 60)
Container.BackgroundTransparency = 1

local SearchPage = Instance.new("Frame", Container); SearchPage.Size = UDim2.new(1,0,1,0); SearchPage.BackgroundTransparency = 1
local ExecutorPage = Instance.new("Frame", Container); ExecutorPage.Size = UDim2.new(1,0,1,0); ExecutorPage.Visible = false; ExecutorPage.BackgroundTransparency = 1
local SettingsPage = Instance.new("ScrollingFrame", Container); SettingsPage.Size = UDim2.new(1,0,1,0); SettingsPage.Visible = false; SettingsPage.BackgroundTransparency = 1; SettingsPage.ScrollBarThickness = 2
local SLayout = Instance.new("UIListLayout", SettingsPage); SLayout.Padding = UDim.new(0, 5)

-- 5. EXECUTOR TAB (Cleaned Header)
local Editor = Instance.new("TextBox", ExecutorPage)
Editor.Size = UDim2.new(1, 0, 0.8, 0)
Editor.BackgroundColor3 = Color3.fromRGB(12, 12, 12)
Editor.Text = "--- RED BLACK HUB ---\n--- DISCORD: https://discord.gg/ffuFGauPdS\n\nprint('Execution Ready')"
Editor.TextColor3 = Color3.fromRGB(200, 200, 200)
Editor.Font = "Code"
Editor.TextSize = 14
Editor.ClearTextOnFocus = false
Editor.MultiLine = true
Editor.TextXAlignment = "Left"; Editor.TextYAlignment = "Top"
Instance.new("UICorner", Editor)

local ExecBtn = Instance.new("TextButton", ExecutorPage)
ExecBtn.Size = UDim2.new(0.48, 0, 0, 45)
ExecBtn.Position = UDim2.new(0, 0, 0.85, 0)
ExecBtn.Text = "RUN"
ExecBtn.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
ExecBtn.TextColor3 = Color3.new(1, 1, 1)
Instance.new("UICorner", ExecBtn)

local ClearBtn = Instance.new("TextButton", ExecutorPage)
ClearBtn.Size = UDim2.new(0.48, 0, 0, 45)
ClearBtn.Position = UDim2.new(0.52, 0, 0.85, 0)
ClearBtn.Text = "CLEAR"
ClearBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
ClearBtn.TextColor3 = Color3.new(1, 1, 1)
Instance.new("UICorner", ClearBtn)

-- 6. SETTINGS DROPDOWN SYSTEM
local function CreateDropdown(title)
    local DropFrame = Instance.new("Frame", SettingsPage)
    DropFrame.Size = UDim2.new(0.95, 0, 0, 35)
    DropFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
    DropFrame.ClipsDescendants = true
    Instance.new("UICorner", DropFrame)
    
    local DropBtn = Instance.new("TextButton", DropFrame)
    DropBtn.Size = UDim2.new(1, 0, 0, 35)
    DropBtn.Text = " > " .. title
    DropBtn.Font = "GothamBold"
    DropBtn.TextColor3 = Color3.new(1, 1, 1)
    DropBtn.BackgroundTransparency = 1
    DropBtn.TextXAlignment = "Left"

    local Content = Instance.new("Frame", DropFrame)
    Content.Size = UDim2.new(1, 0, 0, 0)
    Content.Position = UDim2.new(0, 0, 0, 35)
    Content.BackgroundTransparency = 1
    local CLayout = Instance.new("UIListLayout", Content)

    local isExpanded = false
    DropBtn.MouseButton1Click:Connect(function()
        isExpanded = not isExpanded
        DropFrame.Size = isExpanded and UDim2.new(0.95, 0, 0, CLayout.AbsoluteContentSize.Y + 40) or UDim2.new(0.95, 0, 0, 35)
        DropBtn.Text = isExpanded and " V " .. title or " > " .. title
    end)
    return Content
end

-- Populate Dropdowns
local Visuals = CreateDropdown("VISUAL CUSTOMIZATION")
local function AddOption(parent, text, func)
    local b = Instance.new("TextButton", parent)
    b.Size = UDim2.new(1, 0, 0, 30)
    b.Text = "   - " .. text
    b.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    b.TextColor3 = Color3.new(0.8, 0.8, 0.8)
    b.Font = "Gotham"
    b.TextXAlignment = "Left"
    b.MouseButton1Click:Connect(func)
end

AddOption(Visuals, "Toggle Rainbow RGB", function() Config.RGB_Enabled = not Config.RGB_Enabled end)
AddOption(Visuals, "Toggle Stats (FPS/Ping)", function() Config.Show_Stats = not Config.Show_Stats end)
AddOption(Visuals, "Glass Mode (50% Opacity)", function() Main.BackgroundTransparency = 0.5 end)
AddOption(Visuals, "Solid Mode", function() Main.BackgroundTransparency = 0 end)

local Themes = CreateDropdown("THEME PRESETS")
AddOption(Themes, "Blood Red", function() Config.Main_Color = Color3.fromRGB(255, 0, 0) end)
AddOption(Themes, "Electric Blue", function() Config.Main_Color = Color3.fromRGB(0, 150, 255) end)
AddOption(Themes, "Void Purple", function() Config.Main_Color = Color3.fromRGB(150, 0, 255) end)

local Utils = CreateDropdown("SERVER UTILITIES")
AddOption(Utils, "Rejoin Server", function() game:GetService("TeleportService"):Teleport(game.PlaceId, LP) end)
AddOption(Utils, "Kill Roblox", function() game:Shutdown() end)

-- 7. SEARCH SYSTEM (ScriptBlox)
local SearchInput = Instance.new("TextBox", SearchPage)
SearchInput.Size = UDim2.new(1, 0, 0, 40)
SearchInput.PlaceholderText = "Search Scripts..."
SearchInput.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
SearchInput.TextColor3 = Color3.new(1, 1, 1)
Instance.new("UICorner", SearchInput)

local ResultList = Instance.new("ScrollingFrame", SearchPage)
ResultList.Size = UDim2.new(1, 0, 1, -50)
ResultList.Position = UDim2.new(0, 0, 0, 50)
ResultList.BackgroundTransparency = 1
Instance.new("UIListLayout", ResultList).Padding = UDim.new(0, 10)

local function SearchScripts(query)
    for _, v in pairs(ResultList:GetChildren()) do if v:IsA("Frame") then v:Destroy() end end
    local s, res = pcall(function() return game:HttpGet("https://scriptblox.com/api/script/search?q=" .. HttpService:UrlEncode(query)) end)
    if s then
        local data = HttpService:JSONDecode(res)
        for _, sd in pairs(data.result.scripts) do
            local Card = Instance.new("Frame", ResultList)
            Card.Size = UDim2.new(1, -10, 0, 110)
            Card.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
            Instance.new("UICorner", Card)

            local T = Instance.new("TextLabel", Card)
            T.Size = UDim2.new(1, -20, 0, 25)
            T.Position = UDim2.new(0, 10, 0, 5)
            T.Text = sd.title; T.TextColor3 = Color3.fromRGB(255, 50, 50); T.Font = "GothamBold"; T.BackgroundTransparency = 1; T.TextXAlignment = "Left"

            local S = Instance.new("TextLabel", Card)
            S.Size = UDim2.new(1, -20, 0, 20)
            S.Position = UDim2.new(0, 10, 0, 30)
            S.Text = "👍 " .. sd.likeCount .. " | 👎 " .. sd.dislikeCount .. " | 👁️ " .. sd.views; S.TextColor3 = Color3.fromRGB(150, 150, 150); S.BackgroundTransparency = 1; S.TextXAlignment = "Left"

            local function Act(text, x, color, func)
                local b = Instance.new("TextButton", Card)
                b.Size = UDim2.new(0, 90, 0, 30); b.Position = UDim2.new(0, x, 0, 70); b.Text = text; b.BackgroundColor3 = color; b.TextColor3 = Color3.new(1, 1, 1); Instance.new("UICorner", b); b.MouseButton1Click:Connect(func)
            end
            Act("LOAD", 10, Color3.fromRGB(150, 0, 0), function() Editor.Text = sd.script ExecutorPage.Visible = true SearchPage.Visible = false end)
            Act("EXECUTE", 110, Color3.fromRGB(0, 120, 0), function() loadstring(sd.script)() end)
            Act("COPY", 210, Color3.fromRGB(60, 60, 60), function() setclipboard(sd.script) end)
        end
        ResultList.CanvasSize = UDim2.
