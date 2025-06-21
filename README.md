local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local Humanoid = Character:WaitForChild("Humanoid")

local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- UI Setup
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "SpeedControlUI"
screenGui.ResetOnSpawn = false
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
screenGui.Parent = PlayerGui

-- Frame
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 220, 0, 130)
mainFrame.Position = UDim2.new(0, 20, 0.5, -65)
mainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
mainFrame.BorderSizePixel = 0
mainFrame.BackgroundTransparency = 0.1
mainFrame.Parent = screenGui

-- Title
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 30)
title.BackgroundTransparency = 1
title.Text = "🚀 Speed Control"
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.GothamBold
title.TextScaled = true
title.Parent = mainFrame

-- Speed Slider (0 - 200)
local slider = Instance.new("TextBox")
slider.Size = UDim2.new(0.7, 0, 0, 30)
slider.Position = UDim2.new(0.15, 0, 0, 50)
slider.Text = "16"
slider.PlaceholderText = "Enter Speed (16 - 200)"
slider.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
slider.TextColor3 = Color3.new(1, 1, 1)
slider.Font = Enum.Font.Gotham
tslider.TextScaled = true
slider.ClearTextOnFocus = false
slider.Parent = mainFrame

-- Toggle Button
local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0.7, 0, 0, 30)
toggleButton.Position = UDim2.new(0.15, 0, 0, 90)
toggleButton.Text = "✅ Enable Speed"
toggleButton.BackgroundColor3 = Color3.fromRGB(35, 100, 35)
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.Font = Enum.Font.GothamBold
toggleButton.TextScaled = true
toggleButton.Parent = mainFrame

local speedEnabled = false
local speedValue = 16

toggleButton.MouseButton1Click:Connect(function()
    speedEnabled = not speedEnabled
    toggleButton.Text = speedEnabled and "❌ Disable Speed" or "✅ Enable Speed"
    toggleButton.BackgroundColor3 = speedEnabled and Color3.fromRGB(100, 35, 35) or Color3.fromRGB(35, 100, 35)
end)

slider.FocusLost:Connect(function(enter)
    if enter then
        local val = tonumber(slider.Text)
        if val and val >= 16 and val <= 200 then
            speedValue = val
        else
            slider.Text = tostring(speedValue)
        end
    end
end)

RunService.RenderStepped:Connect(function()
    if speedEnabled and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = speedValue
    else
        LocalPlayer.Character.Humanoid.WalkSpeed = 16
    end
end)
