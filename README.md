local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

-- สร้าง ScreenGui
local gui = Instance.new("ScreenGui")
gui.Name = "SleekUI"
gui.ResetOnSpawn = false
gui.Parent = PlayerGui

-- Main Frame (UI หลัก)
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 220, 0, 260)
mainFrame.Position = UDim2.new(0, 20, 0, 20)
mainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
mainFrame.BorderSizePixel = 0
mainFrame.AnchorPoint = Vector2.new(0, 0)
mainFrame.Parent = gui
mainFrame.ClipsDescendants = true
mainFrame.ZIndex = 10
mainFrame.BackgroundTransparency = 0.05

-- UI Shadow (ทำให้ดูลึกขึ้น)
local shadow = Instance.new("ImageLabel")
shadow.Size = UDim2.new(1, 20, 1, 20)
shadow.Position = UDim2.new(0, -10, 0, -10)
shadow.BackgroundTransparency = 1
shadow.Image = "rbxassetid://1316045217" -- Shadow Asset
shadow.ImageColor3 = Color3.new(0, 0, 0)
shadow.ImageTransparency = 0.7
shadow.ScaleType = Enum.ScaleType.Slice
shadow.SliceCenter = Rect.new(10, 10, 118, 118)
shadow.Parent = mainFrame

-- Title Text
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 40)
title.BackgroundTransparency = 1
title.Text = "🖤 Sleek ESP UI"
title.Font = Enum.Font.GothamBold
title.TextColor3 = Color3.fromRGB(0, 255, 140)
title.TextScaled = true
title.Parent = mainFrame

-- Function สร้างปุ่มสวยๆ
local function createButton(text, posY)
	local btn = Instance.new("TextButton")
	btn.Size = UDim2.new(1, -20, 0, 40)
	btn.Position = UDim2.new(0, 10, 0, posY)
	btn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
	btn.BorderSizePixel = 0
	btn.AutoButtonColor = false
	btn.Font = Enum.Font.GothamSemibold
	btn.TextColor3 = Color3.fromRGB(0, 255, 140)
	btn.TextScaled = true
	btn.Text = text
	btn.ZIndex = 11
	
	-- Hover effect
	btn.MouseEnter:Connect(function()
		TweenService:Create(btn, TweenInfo.new(0.15), {BackgroundColor3 = Color3.fromRGB(0, 255, 140)}):Play()
		TweenService:Create(btn, TweenInfo.new(0.15), {TextColor3 = Color3.fromRGB(20, 20, 20)}):Play()
	end)
	btn.MouseLeave:Connect(function()
		TweenService:Create(btn, TweenInfo.new(0.15), {BackgroundColor3 = Color3.fromRGB(30, 30, 30)}):Play()
		TweenService:Create(btn, TweenInfo.new(0.15), {TextColor3 = Color3.fromRGB(0, 255, 140)}):Play()
	end)
	btn.Parent = mainFrame
	return btn
end

-- สร้างปุ่มต่างๆ
local toggleESPBtn = createButton("🔍 เปิด ESP", 50)
local toggleFOVBtn = createButton("👁️ เปิด FOV", 100)
local toggleAimbotBtn = createButton("🎯 เปิด Aimbot", 150)

-- สร้างปุ่มพับเก็บ (ขนาดเล็ก)
local toggleFoldBtn = Instance.new("TextButton")
toggleFoldBtn.Size = UDim2.new(0, 40, 0, 40)
toggleFoldBtn.Position = UDim2.new(0, 240, 0, 20)
toggleFoldBtn.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
toggleFoldBtn.BorderSizePixel = 0
toggleFoldBtn.TextColor3 = Color3.fromRGB(0, 255, 140)
toggleFoldBtn.Font = Enum.Font.GothamBold
toggleFoldBtn.TextScaled = true
toggleFoldBtn.Text = "≡"
toggleFoldBtn.Parent = gui
toggleFoldBtn.ZIndex = 11

local isFolded = false
toggleFoldBtn.MouseButton1Click:Connect(function()
	if not isFolded then
		-- พับเก็บ: Slide mainFrame ไปซ้าย + ซ่อนปุ่ม
		TweenService:Create(mainFrame, TweenInfo.new(0.3), {Position = UDim2.new(0, -mainFrame.Size.X.Offset, 0, 20)}):Play()
		isFolded = true
	else
		-- เปิดออก: Slide mainFrame กลับตำแหน่งเดิม
		TweenService:Create(mainFrame, TweenInfo.new(0.3), {Position = UDim2.new(0, 20, 0, 20)}):Play()
		isFolded = false
	end
end)

-- ทำให้ปุ่มพับเก็บลากได้ (มือถือ+เมาส์)
local dragging = false
local dragInput, dragStart, startPos

local function update(input)
	local delta = input.Position - dragStart
	toggleFoldBtn.Position = UDim2.new(
		math.clamp(startPos.X.Scale, 0, 1),
		math.clamp(startPos.X.Offset + delta.X, 0, workspace.CurrentCamera.ViewportSize.X - toggleFoldBtn.AbsoluteSize.X),
		math.clamp(startPos.Y.Scale, 0, 1),
		math.clamp(startPos.Y.Offset + delta.Y, 0, workspace.CurrentCamera.ViewportSize.Y - toggleFoldBtn.AbsoluteSize.Y)
	)
end

toggleFoldBtn.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		dragStart = input.Position
		startPos = toggleFoldBtn.Position

		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

toggleFoldBtn.InputChanged:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
		dragInput = input
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if dragging and input == dragInput then
		update(input)
	end
end)

-- ตัวอย่างการใช้: กดปุ่มแล้วเปลี่ยนข้อความ (แก้ตามสคริปต์คุณ)
local ESPEnabled = false
toggleESPBtn.MouseButton1Click:Connect(function()
	ESPEnabled = not ESPEnabled
	toggleESPBtn.Text = ESPEnabled and "❌ ปิด ESP" or "🔍 เปิด ESP"
end)

local FOVEnabled = false
toggleFOVBtn.MouseButton1Click:Connect(function()
	FOVEnabled = not FOVEnabled
	toggleFOVBtn.Text = FOVEnabled and "❌ ปิด FOV" or "👁️ เปิด FOV"
end)

local AimbotEnabled = false
toggleAimbotBtn.MouseButton1Click:Connect(function()
	AimbotEnabled = not AimbotEnabled
	toggleAimbotBtn.Text = AimbotEnabled and "❌ ปิด Aimbot" or "🎯 เปิด Aimbot"
end)

