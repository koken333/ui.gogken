local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
gui.Name = "ShadowUI"
gui.ResetOnSpawn = false

-- Main Frame
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 400, 0, 300)
frame.Position = UDim2.new(0.5, -200, 0.5, -150)
frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
frame.BorderSizePixel = 0
frame.BackgroundTransparency = 0.1
frame.ClipsDescendants = true
frame.AnchorPoint = Vector2.new(0.5, 0.5)

-- UI Stroke (ขอบเท่ ๆ)
local stroke = Instance.new("UIStroke", frame)
stroke.Color = Color3.fromRGB(0, 200, 255)
stroke.Thickness = 2
stroke.Transparency = 0.2

-- UI Corner (มุมโค้ง)
local corner = Instance.new("UICorner", frame)
corner.CornerRadius = UDim.new(0, 12)

-- Title Text
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 40)
title.BackgroundTransparency = 1
title.Text = "⚡ SHADOW UI"
title.TextColor3 = Color3.fromRGB(0, 255, 255)
title.Font = Enum.Font.GothamBold
title.TextSize = 24

-- Button เท่ ๆ
local button = Instance.new("TextButton", frame)
button.Size = UDim2.new(0.8, 0, 0, 40)
button.Position = UDim2.new(0.1, 0, 0.3, 0)
button.BackgroundColor3 = Color3.fromRGB(0, 100, 150)
button.Text = "เปิดโหมดล่องหน"
button.TextColor3 = Color3.new(1,1,1)
button.Font = Enum.Font.GothamSemibold
button.TextSize = 20

local btnCorner = Instance.new("UICorner", button)
btnCorner.CornerRadius = UDim.new(0, 8)

local btnStroke = Instance.new("UIStroke", button)
btnStroke.Color = Color3.fromRGB(0, 255, 255)
btnStroke.Thickness = 1.5
btnStroke.Transparency = 0.1

button.MouseButton1Click:Connect(function()
    print("เปิดล่องหน")
end)
