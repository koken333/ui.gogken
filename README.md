local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

local RunService = game:GetService("RunService")

-- สร้าง ScreenGui
local gui = Instance.new("ScreenGui")
gui.Name = "SpeedControlUI"
gui.ResetOnSpawn = false
gui.Parent = PlayerGui

-- ปุ่มเปิด/ปิด Speed
local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 140, 0, 40)
toggleButton.Position = UDim2.new(0, 20, 0, 20)
toggleButton.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.Font = Enum.Font.GothamSemibold
toggleButton.TextScaled = true
toggleButton.Text = "เปิดความเร็ววิ่ง"
toggleButton.Parent = gui

-- กล่องใส่ค่าความเร็ว
local speedBox = Instance.new("TextBox")
speedBox.Size = UDim2.new(0, 140, 0, 40)
speedBox.Position = UDim2.new(0, 20, 0, 70)
speedBox.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
speedBox.TextColor3 = Color3.new(1, 1, 1)
speedBox.Font = Enum.Font.GothamSemibold
speedBox.TextScaled = true
speedBox.ClearTextOnFocus = false
speedBox.Text = "16"
speedBox.Parent = gui

local speedEnabled = false
local normalSpeed = 16

local function setSpeed(speed)
	if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
		LocalPlayer.Character:FindFirstChildOfClass("Humanoid").WalkSpeed = speed
	end
end

toggleButton.MouseButton1Click:Connect(function()
	speedEnabled = not speedEnabled
	if speedEnabled then
		local desiredSpeed = tonumber(speedBox.Text)
		if desiredSpeed and desiredSpeed >= 16 and desiredSpeed <= 500 then
			setSpeed(desiredSpeed)
			toggleButton.Text = "ปิดความเร็ววิ่ง"
		else
			speedBox.Text = tostring(normalSpeed)
			setSpeed(normalSpeed)
			toggleButton.Text = "ปิดความเร็ววิ่ง"
		end
	else
		setSpeed(normalSpeed)
		toggleButton.Text = "เปิดความเร็ววิ่ง"
	end
end)

speedBox.FocusLost:Connect(function(enterPressed)
	if enterPressed and speedEnabled then
		local val = tonumber(speedBox.Text)
		if val and val >= 16 and val <= 500 then
			setSpeed(val)
		else
			speedBox.Text = tostring(normalSpeed)
		end
	end
end)

-- Reset ความเร็วตอน Respawn
LocalPlayer.CharacterAdded:Connect(function(char)
	char:WaitForChild("Humanoid").WalkSpeed = speedEnabled and tonumber(speedBox.Text) or normalSpeed
end)
