-- Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- Settings
getgenv().ESPEnabled = false
local ESPColor = Color3.fromRGB(0, 255, 255)

-- UI Creation Helper
local function createUI()
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "ModernESP_UI"
    screenGui.ResetOnSpawn = false
    screenGui.Parent = PlayerGui

    -- Main Frame
    local mainFrame = Instance.new("Frame")
    mainFrame.Size = UDim2.new(0, 180, 0, 50)
    mainFrame.Position = UDim2.new(0, 20, 0, 20)
    mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 30)
    mainFrame.BorderSizePixel = 0
    mainFrame.AnchorPoint = Vector2.new(0, 0)
    mainFrame.Parent = screenGui
    mainFrame.ClipsDescendants = true
    mainFrame.Rotation = 0

    -- UI Shadow Effect
    local shadow = Instance.new("Frame")
    shadow.Size = UDim2.new(1, 10, 1, 10)
    shadow.Position = UDim2.new(0, -5, 0, -5)
    shadow.BackgroundColor3 = Color3.fromRGB(0, 255, 255)
    shadow.BackgroundTransparency = 0.8
    shadow.BorderSizePixel = 0
    shadow.ZIndex = 0
    shadow.Rotation = 0
    shadow.Parent = mainFrame

    -- Button
    local toggleButton = Instance.new("TextButton")
    toggleButton.Size = UDim2.new(1, 0, 1, 0)
    toggleButton.BackgroundColor3 = Color3.fromRGB(10, 10, 20)
    toggleButton.BorderSizePixel = 0
    toggleButton.Font = Enum.Font.GothamBold
    toggleButton.TextColor3 = Color3.fromRGB(0, 255, 255)
    toggleButton.TextSize = 22
    toggleButton.Text = "🔍 เปิด ESP"
    toggleButton.Parent = mainFrame
    toggleButton.AutoButtonColor = false

    -- Hover effect
    toggleButton.MouseEnter:Connect(function()
        toggleButton.BackgroundColor3 = Color3.fromRGB(0, 255, 255)
        toggleButton.TextColor3 = Color3.fromRGB(10, 10, 20)
    end)
    toggleButton.MouseLeave:Connect(function()
        toggleButton.BackgroundColor3 = Color3.fromRGB(10, 10, 20)
        toggleButton.TextColor3 = Color3.fromRGB(0, 255, 255)
    end)

    -- Drag functionality
    local dragging, dragInput, dragStart, startPos
    local function update(input)
        local delta = input.Position - dragStart
        mainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X,
            startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
    mainFrame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = mainFrame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    mainFrame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement then
            dragInput = input
        end
    end)
    game:GetService("UserInputService").InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            update(input)
        end
    end)

    return toggleButton
end

-- ESP Logic
local function createESP(player)
    if player == LocalPlayer then return end

    local function onCharacterAdded(character)
        local head = character:WaitForChild("Head", 10)
        if not head then return end

        local oldTag = character:FindFirstChild("ESP_Tag")
        if oldTag then oldTag:Destroy() end

        local tag = Instance.new("BillboardGui")
        tag.Name = "ESP_Tag"
        tag.Adornee = head
        tag.Size = UDim2.new(0, 120, 0, 30)
        tag.StudsOffset = Vector3.new(0, 2.8, 0)
        tag.AlwaysOnTop = true
        tag.Parent = character

        local label = Instance.new("TextLabel", tag)
        label.Size = UDim2.new(1, 0, 1, 0)
        label.BackgroundTransparency = 0.3
        label.BackgroundColor3 = Color3.fromRGB(0, 255, 255)
        label.TextColor3 = Color3.fromRGB(10, 10, 20)
        label.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
        label.TextStrokeTransparency = 0.4
        label.TextScaled = true
        label.Font = Enum.Font.GothamBold
        label.Text = player.Name
        label.BorderSizePixel = 0

        local tweenService = game:GetService("TweenService")
        local info = TweenInfo.new(1.5, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true)

        local tween = tweenService:Create(label, info, {TextColor3 = Color3.fromRGB(0, 255, 255)})
        tween:Play()

        RunService.RenderStepped:Connect(function()
            if getgenv().ESPEnabled and character.Parent and character:FindFirstChild("HumanoidRootPart") and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                local dist = (character.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
                label.Text = player.Name .. " [" .. math.floor(dist) .. "m]"
                tag.Enabled = true
            else
                tag.Enabled = false
            end
        end)
    end

    player.CharacterAdded:Connect(onCharacterAdded)
    if player.Character then
        onCharacterAdded(player.Character)
    end
end

-- Main --
local toggleButton = createUI()

toggleButton.MouseButton1Click:Connect(function()
    getgenv().ESPEnabled = not getgenv().ESPEnabled
    toggleButton.Text = getgenv().ESPEnabled and "❌ ปิด ESP" or "🔍 เปิด ESP"
end)

for _, player in pairs(Players:GetPlayers()) do
    createESP(player)
end
Players.PlayerAdded:Connect(createESP)
