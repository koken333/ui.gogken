local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- ฟังก์ชันปรับขนาดตัวละคร
local function setCharacterSize(scaleValue)
	local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
	local humanoid = character:WaitForChild("Humanoid")
	for _, scaleName in ipairs({"BodyHeightScale", "BodyDepthScale", "BodyWidthScale", "HeadScale"}) do
		local scale = humanoid:FindFirstChild(scaleName)
		if scale then
			scale.Value = scaleValue
		end
	end
end

-- UI สร้างหน้าต่าง
local screenGui = Instance.new("ScreenGui", PlayerGui)
screenGui.Name = "Resize_UI"
screenGui.ResetOnSpawn = false

-- ปุ่มย่อขนาด
local shrinkBtn = Instance.new("TextButton")
shrinkBtn.Size = UDim2.new(0, 150, 0, 40)
shrinkBtn.Position = UDim2.new(0, 20, 0, 60)
shrinkBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
shrinkBtn.TextColor3 = Color3.new(1, 1, 1)
shrinkBtn.Font = Enum.Font.GothamBold
shrinkBtn.TextSize = 18
shrinkBtn.Text = "ย่อขนาด (50%)"
shrinkBtn.Parent = screenGui

-- ปุ่มคืนขนาดปกติ
local resetBtn = shrinkBtn:Clone()
resetBtn.Position = UDim2.new(0, 20, 0, 110)
resetBtn.Text = "คืนขนาด (100%)"
resetBtn.Parent = screenGui

-- ฟังก์ชันปุ่ม
shrinkBtn.MouseButton1Click:Connect(function()
	setCharacterSize(0.5)
end)

resetBtn.MouseButton1Click:Connect(function()
	setCharacterSize(1)
end)
