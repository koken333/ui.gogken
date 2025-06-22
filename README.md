-- Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local Camera = workspace.CurrentCamera

-- ฟังก์ชันสร้าง UI
local function createButton(parent, name, text, posY)
    local btn = Instance.new("TextButton")
    btn.Name = name
    btn.Size = UDim2.new(0, 150, 0, 40)
    btn.Position = UDim2.new(0, 20, 0, posY)
    btn.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
    btn.BorderSizePixel = 0
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.Font = Enum.Font.GothamBold
    btn.TextScaled = true
    btn.Text = text
    btn.Parent = parent
    return btn
end

local function createLabel(parent, text, posY)
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(0, 150, 0, 30)
    lbl.Position = UDim2.new(0, 20, 0, posY)
    lbl.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
    lbl.BorderSizePixel = 0
    lbl.TextColor3 = Color3.new(1, 1, 1)
    lbl.Font = Enum.Font.GothamSemibold
    lbl.TextScaled = true
    lbl.Text = text
    lbl.Parent = parent
    return lbl
end

-- ====== ESP ======
getgenv().ESPEnabled = false
local ESPColor = Color3.fromRGB(0, 255, 0)

local espGui = Instance.new("ScreenGui", PlayerGui)
espGui.Name = "ESP_UI"
espGui.ResetOnSpawn = false
espGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

local toggleESPButton = createButton(espGui, "ToggleESPButton", "🔍 เปิด ESP", 20)
toggleESPButton.MouseButton1Click:Connect(function()
    getgenv().ESPEnabled = not getgenv().ESPEnabled
    toggleESPButton.Text = getgenv().ESPEnabled and "❌ ปิด ESP" or "🔍 เปิด ESP"
end)

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
        tag.Size = UDim2.new(0, 120, 0, 25)
        tag.StudsOffset = Vector3.new(0, 2.7, 0)
        tag.AlwaysOnTop = true
        tag.Parent = character

        local label = Instance.new("TextLabel", tag)
        label.Size = UDim2.new(1, 0, 1, 0)
        label.BackgroundTransparency = 1
        label.TextColor3 = ESPColor
        label.TextStrokeTransparency = 0.4
        label.TextScaled = true
        label.Font = Enum.Font.GothamSemibold
        label.Text = player.Name

        RunService.RenderStepped:Connect(function()
            if getgenv().ESPEnabled 
               and character.Parent 
               and character:FindFirstChild("HumanoidRootPart") 
               and LocalPlayer.Character 
               and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") 
            then
                local dist = (character.HumanoidRootPart.Position 
                             - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
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

for _, p in pairs(Players:GetPlayers()) do
    createESP(p)
end
Players.PlayerAdded:Connect(createESP)

-- ====== TOGGLE UI VISIBILITY (พับ/แสดงเมนู) ======
local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- สร้าง ScreenGui
local screenGui = Instance.new("ScreenGui", PlayerGui)
screenGui.Name = "LogoUI"
screenGui.ResetOnSpawn = false

-- สร้างเฟรมโลโก้/ไมค์
local logoFrame = Instance.new("Frame", screenGui)
logoFrame.Size = UDim2.new(0, 80, 0, 80)
logoFrame.Position = UDim2.new(0.5, -40, 0.5, -40)
logoFrame.AnchorPoint = Vector2.new(0.5, 0.5)
logoFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
logoFrame.BackgroundTransparency = 0.3
logoFrame.BorderSizePixel = 0
logoFrame.Name = "RoundedBG"

local uicorner = Instance.new("UICorner", logoFrame)
uicorner.CornerRadius = UDim.new(0, 40)

-- ใส่รูปภาพในเฟรม
local imageLabel = Instance.new("ImageLabel", logoFrame)
imageLabel.Size = UDim2.new(1, 0, 1, 0)
imageLabel.BackgroundTransparency = 1
imageLabel.Image = "rbxassetid://14737398259"

-- ========= ฟังก์ชันลาก UI =========
local dragging = false
local dragStart, startPos

-- เมื่อเริ่มกด/แตะที่เฟรม
logoFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or
       input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = logoFrame.Position

        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

-- บันทึก event การเคลื่อนเมาส์
logoFrame.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or
       input.UserInputType == Enum.UserInputType.Touch then
        dragInput = input
    end
end)

-- ติดตามและอัปเดตตำแหน่งเมื่อมีการลาก
UIS.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        local delta = input.Position - dragStart
        logoFrame.Position = UDim2.new(
            startPos.X.Scale, startPos.X.Offset + delta.X,
            startPos.Y.Scale, startPos.Y.Offset + delta.Y
        )
    end
end)
