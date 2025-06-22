local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- UI Setup
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "SpectateUI"
screenGui.Parent = game.CoreGui  -- ใช้ CoreGui แทน PlayerGui เพื่อให้รันบน KRNL ได้
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true

-- ปุ่มเปิด UI
local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 140, 0, 40)
toggleButton.Position = UDim2.new(0, 20, 0, 20)
toggleButton.BackgroundColor3 = Color3.fromRGB(30, 180, 255)
toggleButton.Text = "ดูผู้เล่น"
toggleButton.TextScaled = true
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.Font = Enum.Font.SourceSansBold
toggleButton.Parent = screenGui

-- เฟรมรายชื่อ
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 220, 0, 300)
frame.Position = UDim2.new(0, 20, 0, 70)
frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
frame.Visible = false
frame.Parent = screenGui

local uiList = Instance.new("UIListLayout", frame)
uiList.Padding = UDim.new(0, 4)
uiList.SortOrder = Enum.SortOrder.LayoutOrder

-- ฟังก์ชันดูจอผู้เล่น
local function spectatePlayer(target)
    if target and target.Character and target.Character:FindFirstChild("Humanoid") then
        Camera.CameraSubject = target.Character.Humanoid
    end
end

-- สร้างปุ่มสำหรับแต่ละผู้เล่น
local function updateList()
    for _, child in ipairs(frame:GetChildren()) do
        if child:IsA("TextButton") then
            child:Destroy()
        end
    end

    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer then
            local btn = Instance.new("TextButton")
            btn.Size = UDim2.new(1, -8, 0, 40)
            btn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
            btn.Text = p.Name
            btn.TextColor3 = Color3.new(1, 1, 1)
            btn.TextScaled = true
            btn.Font = Enum.Font.SourceSansBold
            btn.Parent = frame

            btn.MouseButton1Click:Connect(function()
                spectatePlayer(p)
            end)
        end
    end
end

-- ปุ่มกดเปิด/ปิด UI
toggleButton.MouseButton1Click:Connect(function()
    frame.Visible = not frame.Visible
    if frame.Visible then
        updateList()
    end
end)

-- อัปเดตรายชื่อหากมีผู้เล่นเข้า/ออก
Players.PlayerAdded:Connect(function()
    if frame.Visible then updateList() end
end)
Players.PlayerRemoving:Connect(function()
    if frame.Visible then updateList() end
end)

