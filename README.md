-- Matrix Hub Ultimate All-In-One Hyper-Quantum Supreme Suite (Luau / Roblox)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local CoreGui = game:GetService("CoreGui")

local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Configuration State with Potato Graphics Mode & Smart Auto-Toggle/Switching
local Config = {
    MenuVisible = true,
    AimbotEnabled = false,
    TeamCheck = true,
    ESPEnabled = true,
    SkeletonEnabled = true,
    SnaplinesEnabled = true,
    Box2D = true,
    CornerBox = true,
    FOVEnabled = true,
    FOVSize = 130,
    RainbowSpeed = 0.1,
    SpinningCrosshair = true,
    CrosshairSpeed = 4.0,
    PotatoGraphics = false,
    TargetBone = "Head"
}

-- Target tracking state
local currentLockedPlayer = nil

-- Create Core GUI Container
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "MatrixHubSupremeAllInOne"
ScreenGui.Parent = CoreGui
ScreenGui.IgnoreGuiInset = true

-- Main Draggable Window Frame (Toggle with H)
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 600, 0, 420)
MainFrame.Position = UDim2.new(0.5, -300, 0.5, -210)
MainFrame.BackgroundColor3 = Color3.fromRGB(8, 8, 10)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 10)
MainCorner.Parent = MainFrame

local MainStroke = Instance.new("UIStroke")
MainStroke.Thickness = 2
MainStroke.Color = Color3.fromRGB(0, 255, 65)
MainStroke.Parent = MainFrame

-- Top Gaming Header Bar
local HeaderBar = Instance.new("Frame")
HeaderBar.Size = UDim2.new(1, 0, 0, 36)
HeaderBar.BackgroundColor3 = Color3.fromRGB(15, 35, 15)
HeaderBar.BorderSizePixel = 0
HeaderBar.Parent = MainFrame

local HeaderCorner = Instance.new("UICorner")
HeaderCorner.CornerRadius = UDim.new(0, 10)
HeaderCorner.Parent = HeaderBar

local HeaderTitle = Instance.new("TextLabel")
HeaderTitle.Size = UDim2.new(1, -20, 1, 0)
HeaderTitle.Position = UDim2.new(0, 15, 0, 0)
HeaderTitle.BackgroundTransparency = 1
HeaderTitle.Text = "MATRIX HUB // ALL-IN-ONE SUPREME [H: MENU]"
HeaderTitle.TextColor3 = Color3.fromRGB(0, 255, 65)
HeaderTitle.TextSize = 12
HeaderTitle.Font = Enum.Font.Code
HeaderTitle.TextXAlignment = Enum.TextXAlignment.Left
HeaderTitle.Parent = HeaderBar

-- Sidebar Category Panel
local Sidebar = Instance.new("Frame")
Sidebar.Size = UDim2.new(0, 160, 1, -36)
Sidebar.Position = UDim2.new(0, 0, 0, 36)
Sidebar.BackgroundColor3 = Color3.fromRGB(12, 12, 15)
Sidebar.BorderSizePixel = 0
Sidebar.Parent = MainFrame

-- Right Content Panel
local ContentPanel = Instance.new("Frame")
ContentPanel.Size = UDim2.new(1, -160, 1, -36)
ContentPanel.Position = UDim2.new(0, 160, 0, 36)
ContentPanel.BackgroundColor3 = Color3.fromRGB(6, 6, 8)
ContentPanel.BorderSizePixel = 0
ContentPanel.Parent = MainFrame

local ContentLayoutTitle = Instance.new("TextLabel")
ContentLayoutTitle.Size = UDim2.new(1, 0, 0, 32)
ContentLayoutTitle.Position = UDim2.new(0, 18, 0, 5)
ContentLayoutTitle.BackgroundTransparency = 1
ContentLayoutTitle.Text = "-> Combat <-"
ContentLayoutTitle.TextColor3 = Color3.fromRGB(180, 180, 200)
ContentLayoutTitle.TextSize = 11
ContentLayoutTitle.Font = Enum.Font.Code
ContentLayoutTitle.TextXAlignment = Enum.TextXAlignment.Left
ContentLayoutTitle.Parent = ContentPanel

local function CreateCategoryButton(name, posY, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.88, 0, 0, 40)
    btn.Position = UDim2.new(0.06, 0, 0, posY)
    btn.BackgroundColor3 = Color3.fromRGB(18, 18, 22)
    btn.TextColor3 = Color3.fromRGB(220, 220, 230)
    btn.TextSize = 11
    btn.Font = Enum.Font.Code
    btn.Text = name
    btn.Parent = Sidebar

    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 6)
    corner.Parent = btn

    btn.MouseButton1Click:Connect(callback)
    return btn
end

local function CreateToggle(name, posX, posY, initialVal, callback)
    local toggleBtn = Instance.new("TextButton")
    toggleBtn.Size = UDim2.new(0, 16, 0, 16)
    toggleBtn.Position = UDim2.new(0, posX, 0, posY)
    toggleBtn.BackgroundColor3 = Color3.fromRGB(16, 16, 20)
    toggleBtn.BorderColor3 = Color3.fromRGB(80, 80, 100)
    toggleBtn.Text = initialVal and "✓" or ""
    toggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    toggleBtn.TextSize = 11
    toggleBtn.Font = Enum.Font.Code
    toggleBtn.Parent = ContentPanel

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0, 170, 0, 16)
    label.Position = UDim2.new(0, posX + 24, 0, posY)
    label.BackgroundTransparency = 1
    label.Text = name
    label.TextColor3 = Color3.fromRGB(200, 200, 210)
    label.TextSize = 11
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Font = Enum.Font.Code
    label.Parent = ContentPanel

    local state = initialVal
    toggleBtn.MouseButton1Click:Connect(function()
        state = not state
        toggleBtn.Text = state and "✓" or ""
        callback(state)
    end)
end

local function CreateSlider(name, posX, posY, minVal, maxVal, currentVal, callback)
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0, 210, 0, 16)
    label.Position = UDim2.new(0, posX, 0, posY)
    label.BackgroundTransparency = 1
    label.Text = name .. ": " .. tostring(currentVal)
    label.TextColor3 = Color3.fromRGB(200, 200, 210)
    label.TextSize = 11
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Font = Enum.Font.Code
    label.Parent = ContentPanel

    local downBtn = Instance.new("TextButton")
    downBtn.Size = UDim2.new(0, 24, 0, 18)
    downBtn.Position = UDim2.new(0, posX + 220, 0, posY)
    downBtn.BackgroundColor3 = Color3.fromRGB(25, 25, 32)
    downBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    downBtn.Text = "-"
    downBtn.TextSize = 11
    downBtn.Font = Enum.Font.Code
    downBtn.Parent = ContentPanel

    local upBtn = Instance.new("TextButton")
    upBtn.Size = UDim2.new(0, 24, 0, 18)
    upBtn.Position = UDim2.new(0, posX + 250, 0, posY)
    upBtn.BackgroundColor3 = Color3.fromRGB(25, 25, 32)
    upBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    upBtn.Text = "+"
    upBtn.TextSize = 11
    upBtn.Font = Enum.Font.Code
    upBtn.Parent = ContentPanel

    local val = currentVal
    downBtn.MouseButton1Click:Connect(function()
        val = math.clamp(val - 15, minVal, maxVal)
        label.Text = name .. ": " .. tostring(val)
        callback(val)
    end)

    upBtn.MouseButton1Click:Connect(function()
        val = math.clamp(val + 15, minVal, maxVal)
        label.Text = name .. ": " .. tostring(val)
        callback(val)
    end)
end

local function ClearContent()
    for _, child in ipairs(ContentPanel:GetChildren()) do
        if child:IsA("TextButton") or child:IsA("TextBox") or (child:IsA("TextLabel") and child ~= ContentLayoutTitle) then
            child:Destroy()
        end
    end
end

local function LoadCombatView()
    ContentLayoutTitle.Text = "-> Combat (E: ON / R: OFF) <-"
    ClearContent()
    CreateToggle("Aimbot Enabled", 25, 45, Config.AimbotEnabled, function(v) Config.AimbotEnabled = v end)
    CreateToggle("Team Checker", 25, 80, Config.TeamCheck, function(v) Config.TeamCheck = v end)
    CreateSlider("FOV Radius", 25, 120, 30, 350, Config.FOVSize, function(v) Config.FOVSize = v end)
    CreateToggle("FOV Circle Visible", 25, 160, Config.FOVEnabled, function(v) Config.FOVEnabled = v end)
end

local function LoadRenderingView()
    ContentLayoutTitle.Text = "-> Rendering & ESP <-"
    ClearContent()
    CreateToggle("Master ESP Toggle", 25, 45, Config.ESPEnabled, function(v) Config.ESPEnabled = v end)
    CreateToggle("2D Box ESP", 25, 80, Config.Box2D, function(v) Config.Box2D = v end)
    CreateToggle("Skeleton ESP", 210, 80, Config.SkeletonEnabled, function(v) Config.SkeletonEnabled = v end)
    CreateToggle("Snaplines", 25, 115, Config.SnaplinesEnabled, function(v) Config.SnaplinesEnabled = v end)
    CreateToggle("Corner Box ESP", 210, 115, Config.CornerBox, function(v) Config.CornerBox = v end)
end

local function LoadExploitsView()
    ContentLayoutTitle.Text = "-> Performance & Visuals <-"
    ClearContent()
    CreateToggle("Potato Graphics Mode", 25, 45, Config.PotatoGraphics, function(v) 
        Config.PotatoGraphics = v 
        if v then
            Lighting.GlobalShadows = false
            Lighting.Brightness = 2
            for _, obj in ipairs(workspace:GetDescendants()) do
                if obj:IsA("BasePart") then
                    obj.Material = Enum.Material.SmoothPlastic
                    obj.Reflectance = 0
                end
            end
        else
            Lighting.GlobalShadows = true
        end
    end)
    CreateToggle("Spinning Crosshair", 25, 85, Config.SpinningCrosshair, function(v) Config.SpinningCrosshair = v end)
end

local function LoadDiscordView()
    ContentLayoutTitle.Text = "-> Discord Community <-"
    ClearContent()
    
    local infoLabel = Instance.new("TextLabel")
    infoLabel.Size = UDim2.new(0, 350, 0, 40)
    infoLabel.Position = UDim2.new(0, 25, 0, 45)
    infoLabel.BackgroundTransparency = 1
    infoLabel.Text = "Join our official Matrix Hub Discord server!\nInvite Link:"
    infoLabel.TextColor3 = Color3.fromRGB(200, 200, 220)
    infoLabel.TextSize = 11
    infoLabel.TextXAlignment = Enum.TextXAlignment.Left
    infoLabel.Font = Enum.Font.Code
    infoLabel.Parent = ContentPanel

    local linkBox = Instance.new("TextBox")
    linkBox.Size = UDim2.new(0, 320, 0, 32)
    linkBox.Position = UDim2.new(0, 25, 0, 95)
    linkBox.BackgroundColor3 = Color3.fromRGB(15, 15, 20)
    linkBox.BorderColor3 = Color3.fromRGB(0, 255, 65)
    linkBox.TextColor3 = Color3.fromRGB(0, 255, 65)
    linkBox.TextSize = 11
    linkBox.Font = Enum.Font.Code
    linkBox.Text = "https://discord.gg/YFpfGZnDMU"
    linkBox.ClearTextOnFocus = false
    linkBox.Editable = false
    linkBox.Parent = ContentPanel

    local copyBtn = Instance.new("TextButton")
    copyBtn.Size = UDim2.new(0, 120, 0, 30)
    copyBtn.Position = UDim2.new(0, 25, 0, 140)
    copyBtn.BackgroundColor3 = Color3.fromRGB(0, 180, 50)
    copyBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    copyBtn.TextSize = 11
    copyBtn.Font = Enum.Font.Code
    copyBtn.Text = "Copy Invite"
    copyBtn.Parent = ContentPanel

    local copyCorner = Instance.new("UICorner")
    copyCorner.CornerRadius = UDim.new(0, 4)
    copyCorner.Parent = copyBtn

    copyBtn.MouseButton1Click:Connect(function()
        if syn and syn.request then
            syn.request({Url = "http://127.0.0.1:6463/rpc?v=1", Method = "POST"})
        end
        if setclipboard then
            setclipboard("https://discord.gg/YFpfGZnDMU")
            copyBtn.Text = "Copied!"
            task.wait(1.5)
            copyBtn.Text = "Copy Invite"
        end
    end)
end

CreateCategoryButton("Combat", 15, LoadCombatView)
CreateCategoryButton("Rendering", 60, LoadRenderingView)
CreateCategoryButton("Exploits & FPS", 105, LoadExploitsView)
CreateCategoryButton("Discord", 150, LoadDiscordView)

-- Exact Rainbow Spinning Crosshair UI Setup
local CrosshairContainer = Instance.new("Frame")
CrosshairContainer.Size = UDim2.new(0, 36, 0, 36)
CrosshairContainer.Position = UDim2.new(0.5, -18, 0.5, -18)
CrosshairContainer.BackgroundTransparency = 1
CrosshairContainer.Parent = ScreenGui

local function CreateBar(size, pos)
    local b = Instance.new("Frame")
    b.Size = size
    b.Position = pos
    b.BorderSizePixel = 0
    b.Parent = CrosshairContainer
    return b
end

local CoreDot = Instance.new("Frame")
CoreDot.Size = UDim2.new(0, 4, 0, 4)
CoreDot.Position = UDim2.new(0.5, -2, 0.5, -2)
CoreDot.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
CoreDot.BorderSizePixel = 0
CoreDot.Parent = CrosshairContainer

local BarUp = CreateBar(UDim2.new(0, 4, 0, 12), UDim2.new(0.5, -2, 0, 0))
local BarDown = CreateBar(UDim2.new(0, 4, 0, 12), UDim2.new(0.5, -2, 1, -12))
local BarLeft = CreateBar(UDim2.new(0, 12, 0, 4), UDim2.new(0, 0, 0.5, -2))
local BarRight = CreateBar(UDim2.new(0, 12, 0, 4), UDim2.new(1, -12, 0.5, -2))

-- FOV Circle
local FOVCircle = Drawing.new("Circle")
FOVCircle.Visible = true
FOVCircle.Thickness = 1.3
FOVCircle.NumSides = 64
FOVCircle.Radius = Config.FOVSize
FOVCircle.Filled = false

-- Fully Working Skeleton Bone Connections Mapping
local bonePairs = {
    {"Head", "UpperTorso"},
    {"UpperTorso", "LowerTorso"},
    {"UpperTorso", "LeftUpperArm"},
    {"LeftUpperArm", "LeftLowerArm"},
    {"LeftLowerArm", "LeftHand"},
    {"UpperTorso", "RightUpperArm"},
    {"RightUpperArm", "RightLowerArm"},
    {"RightLowerArm", "RightHand"},
    {"LowerTorso", "LeftUpperLeg"},
    {"LeftUpperLeg", "LeftLowerLeg"},
    {"LeftLowerLeg", "LeftFoot"},
    {"LowerTorso", "RightUpperLeg"},
    {"RightUpperLeg", "RightLowerLeg"},
    {"RightLowerLeg", "RightFoot"}
}

-- ESP & Line Cache
local espCache = {}
local lineCache = {}
local skeletonCache = {}

local function CreateESP(player)
    if player == LocalPlayer then return end
    
    -- Box ESP
    local box = Drawing.new("Square")
    box.Visible = false
    box.Thickness = 1.3
    box.Filled = false
    espCache[player] = box

    -- Snapline ESP
    local line = Drawing.new("Line")
    line.Visible = false
    line.Thickness = 1.2
    lineCache[player] = line

    -- Skeleton Lines Cache
    local skelLines = {}
    for _, pair in ipairs(bonePairs) do
        local l = Drawing.new("Line")
        l.Visible = false
        l.Thickness = 1.2
        table.insert(skelLines, {line = l, p1 = pair[1], p2 = pair[2]})
    end
    skeletonCache[player] = skelLines
end

for _, player in ipairs(Players:GetPlayers()) do CreateESP(player) end
Players.PlayerAdded:Connect(CreateESP)
Players.PlayerRemoving:Connect(function(player)
    if espCache[player] then espCache[player]:Remove(); espCache[player] = nil end
    if lineCache[player] then lineCache[player]:Remove(); lineCache[player] = nil end
    if skeletonCache[player] then
        for _, data in ipairs(skeletonCache[player]) do data.line:Remove() end
        skeletonCache[player] = nil
    end
end)

-- Keyboard Input Bindings (H for Menu, E for Aimbot ON, R for Aimbot OFF)
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if input.KeyCode == Enum.KeyCode.H then
        Config.MenuVisible = not Config.MenuVisible
        MainFrame.Visible = Config.MenuVisible
    elseif input.KeyCode == Enum.KeyCode.E then
        Config.AimbotEnabled = true
    elseif input.KeyCode == Enum.KeyCode.R then
        Config.AimbotEnabled = false
        currentLockedPlayer = nil
    end
end)

-- Initialize Default Tab
LoadCombatView()

-- Core High-Performance Engine Loop
RunService.RenderStepped:Connect(function()
    local tickVal = tick()
    local slowHue = (tickVal * Config.RainbowSpeed) % 1
    local rainbowColor = Color3.fromHSV(slowHue, 1, 1)

    -- Dynamic Crosshair Spinning & Rainbow Shifting
    if Config.SpinningCrosshair then
        CrosshairContainer.Rotation = (tickVal * 60 * Config.CrosshairSpeed) % 360
    else
        CrosshairContainer.Rotation = 0
    end

    BarUp.BackgroundColor3 = rainbowColor
    BarDown.BackgroundColor3 = rainbowColor
    BarLeft.BackgroundColor3 = rainbowColor
    BarRight.BackgroundColor3 = rainbowColor

    -- FOV Circle Update
    FOVCircle.Visible = Config.FOVEnabled and Config.MenuVisible
    if Config.FOVEnabled then
        FOVCircle.Radius = Config.FOVSize
        FOVCircle.Position = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
        FOVCircle.Color = rainbowColor
    end

    -- Comprehensive ESP Rendering (Boxes, Snaplines, Skeletons)
    for player, box in pairs(espCache) do
        local character = player.Character
        local isTeammate = Config.TeamCheck and player.Team == LocalPlayer
        local line = lineCache[player]
        local skelList = skeletonCache[player]
        
        if Config.ESPEnabled and not isTeammate and character and character:FindFirstChild("HumanoidRootPart") and character:FindFirstChild("Humanoid") and character.Humanoid.Health > 0 then
            local rootPart = character.HumanoidRootPart
            local vector, onScreen = Camera:WorldToViewportPoint(rootPart.Position)

            if onScreen then
                local head = character:FindFirstChild("Head")
                local top = head and Camera:WorldToViewportPoint(head.Position + Vector3.new(0, 0.5, 0)) or vector
                local bottom = Camera:WorldToViewportPoint(rootPart.Position - Vector3.new(0, 3, 0))
                local height = math.abs(top.Y - bottom.Y)
                local width = height / 2

                -- Box Render
                if Config.Box2D then
                    box.Size = Vector2.new(width, height)
                    box.Position = Vector2.new(vector.X - width / 2, top.Y)
                    box.Color = rainbowColor
                    box.Visible = true
                else
                    box.Visible = false
                end

                -- Snapline Render
                if Config.SnaplinesEnabled and line then
                    line.From = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y)
                    line.To = Vector2.new(vector.X, bottom.Y)
                    line.Color = rainbowColor
                    line.Visible = true
                elseif line then
                    line.Visible = false
                end

                -- Skeleton Render
                if Config.SkeletonEnabled and skelList then
                    for _, skelData in ipairs(skelList) do
                        local p1Obj = character:FindFirstChild(skelData.p1)
                        local p2Obj = character:FindFirstChild(skelData.p2)
                        if p1Obj and p2Obj then
                            local pos1, vis1 = Camera:WorldToViewportPoint(p1Obj.Position)
                            local pos2, vis2 = Camera:WorldToViewportPoint(p2Obj.Position)
                            if vis1 and vis2 then
                                skelData.line.From = Vector2.new(pos1.X, pos1.Y)
                                skelData.line.To = Vector2.new(pos2.X, pos2.Y)
                                skelData.line.Color = rainbowColor
                                skelData.line.Visible = true
                            else
                                skelData.line.Visible = false
                            end
                        else
                            skelData.line.Visible = false
                        end
                    end
                elseif skelList then
                    for _, skelData in ipairs(skelList) do skelData.line.Visible = false end
                end
            else
                box.Visible = false
                if line then line.Visible = false end
                if skelList then for _, d in ipairs(skelList) do d.line.Visible = false end end
            end
        else
            box.Visible = false
            if line then line.Visible = false end
            if skelList then for _, d in ipairs(skelList) do d.line.Visible = false end end
        end
    end

    -- Ultra-Smart Aimbot Engine with Automatic Target Cycling & Auto-Shutoff when no targets exist
    if Config.AimbotEnabled then
        -- Validate current target (if eliminated or invalid, jump to next person)
        if currentLockedPlayer then
            local char = currentLockedPlayer.Character
            local hum = char and char:FindFirstChild("Humanoid")
            local bone = char and char:FindFirstChild(Config.TargetBone)
            if not char or not hum or hum.Health <= 0 or not bone then
                currentLockedPlayer = nil
            end
        end

        local bestTargetBone = nil
        local shortestDist = Config.FOVSize

        -- Maintain lock on current target if valid
        if currentLockedPlayer and currentLockedPlayer.Character and currentLockedPlayer.Character:FindFirstChild(Config.TargetBone) then
            local bone = currentLockedPlayer.Character[Config.TargetBone]
            local screenPos, onScreen = Camera:WorldToViewportPoint(bone.Position)
            if onScreen then
                bestTargetBone = bone
            else
                currentLockedPlayer = nil
            end
        end

        -- Find next valid target if none locked
        if not bestTargetBone then
            local targetsFound = 0
            for _, player in ipairs(Players:GetPlayers()) do
                if player ~= LocalPlayer then
                    local isTeammate = Config.TeamCheck and player.Team == LocalPlayer
                    if not isTeammate and player.Character then
                        local bone = player.Character:FindFirstChild(Config.TargetBone)
                        local humanoid = player.Character:FindFirstChild("Humanoid")
                        if bone and humanoid and humanoid.Health > 0 then
                            targetsFound = targetsFound + 1
                            local screenPos, onScreen = Camera:WorldToViewportPoint(bone.Position)
                            if onScreen then
                                local mouseDist = (Vector2.new(screenPos.X, screenPos.Y) - UserInputService:GetMouseLocation()).Magnitude
                                if mouseDist < shortestDist then
                                    shortestDist = mouseDist
                                    bestTargetBone = bone
                                    currentLockedPlayer = player
                                end
                            end
                        end
                    end
                end
            end

            -- Smart Auto-Shutoff: If there are literally no targets available left in the game, turn aimbot off automatically!
            if targetsFound == 0 then
                Config.AimbotEnabled = false
                currentLockedPlayer = nil
            end
        end

        -- Snap camera to target bone
        if bestTargetBone then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, bestTargetBone.Position)
        end
    end
end)
