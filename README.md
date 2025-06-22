local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- สร้าง ScreenGui สำหรับ UI
local screenGui = Instance.new("ScreenGui", PlayerGui)
screenGui.Name = "ESP_Spectate_UI"
screenGui.ResetOnSpawn = false

-- สร้างปุ่ม Toggle สำหรับเปิด/ปิด UI
local toggleButton = Instance.new("TextButton", screenGui)
toggleButton.Size = UDim2.new(0, 150, 0, 50)
toggleButton.Position = UDim2.new(0, 20, 0, 20)
toggleButton.Text = "เปิด UI"
toggleButton.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleButton.Font = Enum.Font.GothamBold
toggleButton.TextScaled = true

-- สร้าง Frame สำหรับ ESP และ Spectate
local menuFrame = Instance.new("Frame", screenGui)
menuFrame.Size = UDim2.new(0, 200, 0, 300)
menuFrame.Position = UDim2.new(0, 20, 0, 80)
menuFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
menuFrame.Visible = false

-- ปุ่มสำหรับเปิด/ปิด ESP
local espButton = Instance.new("TextButton", menuFrame)
espButton.Size = UDim2.new(0, 150, 0, 50)
espButton.Position = UDim2.new(0, 20, 0, 20)
espButton.Text = "เปิด ESP"
espButton.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
espButton.TextColor3 = Color3.fromRGB(255, 255, 255)
espButton.Font = Enum.Font.GothamBold
espButton.TextScaled = true

-- ปุ่มสำหรับเปิด/ปิด Spectate
local spectateButton = Instance.new("TextButton", menuFrame)
spectateButton.Size = UDim2.new(0, 150, 0, 50)
spectateButton.Position = UDim2.new(0, 20, 0, 80)
spectateButton.Text = "เปิด Spectate"
spectateButton.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
spectateButton.TextColor3 = Color3.fromRGB(255, 255, 255)
spectateButton.Font = Enum.Font.GothamBold
spectateButton.TextScaled = true

-- ปุ่มสำหรับปิด UI
local closeButton = Instance.new("TextButton", menuFrame)
closeButton.Size = UDim2.new(0, 150, 0, 50)
closeButton.Position = UDim2.new(0, 20, 0, 140)
closeButton.Text = "ปิด UI"
closeButton.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.Font = Enum.Font.GothamBold
closeButton.TextScaled = true

-- ตัวแปรสำหรับ ESP และ Spectate
local espEnabled = false
local spectateEnabled = false
local currentSpectatingPlayer = nil

-- ฟังก์ชันสำหรับ Toggle UI
toggleButton.MouseButton1Click:Connect(function()
    menuFrame.Visible = not menuFrame.Visible
    toggleButton.Text = menuFrame.Visible and "ปิด UI" or "เปิด UI"
end)

-- ฟังก์ชันสำหรับ Toggle ESP
espButton.MouseButton1Click:Connect(function()
    espEnabled = not espEnabled
    espButton.Text = espEnabled and "ปิด ESP" or "เปิด ESP"
end)

-- ฟังก์ชันสำหรับ Toggle Spectate
spectateButton.MouseButton1Click:Connect(function()
    spectateEnabled = not spectateEnabled
    spectateButton.Text = spectateEnabled and "ปิด Spectate" or "เปิด Spectate"
    if spectateEnabled then
        -- เลือกผู้เล่นคนแรกในเกมเพื่อเริ่มการ Spectate
        local players = Players:GetPlayers()
        for _, player in ipairs(players) do
            if player ~= LocalPlayer then
                currentSpectatingPlayer = player
                Camera.CameraSubject = player.Character.Humanoid
                Camera.CameraType = Enum.CameraType.Scriptable
                break
            end
        end
    else
        Camera.CameraSubject = LocalPlayer.Character.Humanoid
        Camera.CameraType = Enum.CameraType.Custom
    end
end)

-- ฟังก์ชันสำหรับสร้าง ESP
local function createESP(player)
    if player == LocalPlayer then return end

    local tag = Instance.new("BillboardGui")
    tag.Name = "ESP_Tag"
    tag.Adornee = player.Character:WaitForChild("Head")
    tag.Size = UDim2.new(0, 120, 0, 25)
    tag.StudsOffset = Vector3.new(0, 2.7, 0)
    tag.AlwaysOnTop = true
    tag.Parent = player.Character

    local label = Instance.new("TextLabel", tag)
    label.Size = UDim2.new(1, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.TextColor3 = Color3.fromRGB(255, 255, 255)
    label.TextStrokeTransparency = 0.4
    label.TextScaled = true
    label.Font = Enum.Font.GothamSemibold
    label.Text = player.Name

    RunService.RenderStepped:Connect(function()
        if espEnabled and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local dist = (player.Character.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
            label.Text = player.Name .. " [" .. math.floor(dist) .. "m]"
            tag.Enabled = true
        else
            tag.Enabled = false
        end
    end)
end

-- สร้าง ESP สำหรับผู้เล่นที่มีอยู่แล้ว
for _, player in ipairs(Players:GetPlayers()) do
    createESP(player)
end

-- สร้าง ESP เมื่อมีผู้เล่นใหม่เข้ามา
Players.PlayerAdded:Connect(createESP)

-- ลบ ESP เมื่อผู้เล่นออกจากเกม
Players.PlayerRemoving:Connect(function(player)
    local tag = player.Character and player.Character:FindFirstChild("ESP_Tag")
    if tag then
        tag:Destroy()
    end
end)

-- ฟังก์ชันสำหรับการ Spectate
RunService.RenderStepped:Connect(function()
    if spectateEnabled and currentSpectatingPlayer and currentSpectatingPlayer.Character then
        Camera.CFrame = Camera.CFrame:Lerp(CFrame.new(currentSpectatingPlayer.Character.HumanoidRootPart.Position + Vector3.new(0, 5, 0), currentSpectatingPlayer.Character.HumanoidRootPart.Position), 0.1)
    end
end)

