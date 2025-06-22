local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- UI หลัก
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "LogoUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = PlayerGui

-- พื้นหลังดำ + มุมโค้ง
local logoFrame = Instance.new("Frame")
logoFrame.Size = UDim2.new(0, 20, 0, 20)
logoFrame.Position = UDim2.new(0.5, -200, 0.5, -100)
logoFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
logoFrame.BorderSizePixel = 0
logoFrame.BackgroundTransparency = 0
logoFrame.ClipsDescendants = true
logoFrame.Parent = screenGui
logoFrame.AnchorPoint = Vector2.new(0.5, 0.5)
logoFrame.Name = "RoundedBG"

-- มุมโค้ง
local uicorner = Instance.new("UICorner", logoFrame)
uicorner.CornerRadius = UDim.new(0, 40)

-- ใส่ภาพโลโก้ (ใช้ Asset ID ที่คุณอัปโหลดเอง)
local imageLabel = Instance.new("ImageLabel")
imageLabel.Size = UDim2.new(1, 0, 1, 0)
imageLabel.BackgroundTransparency = 1
imageLabel.Image = "rbxassetid://14737398259" -- แทนที่ด้วย Asset ID ของภาพคุณ
imageLabel.Parent = logoFrame
