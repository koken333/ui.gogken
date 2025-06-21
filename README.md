local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

getgenv().ESP_XRay = true

-- UI เปิด/ปิด ESP
local gui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
gui.Name = "XRayESP_UI"

local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 130, 0, 40)
toggleButton.Position = UDim2.new(0, 20, 0, 20)
toggleButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
toggleButton.TextColor3 = Color3.new(1, 1, 1)
toggleButton.Text = "👁️ เปิด X-Ray ESP"
toggleButton.Font = Enum.Font.SourceSansBold
toggleButton.TextScaled = true
toggleButton.Parent = gui

toggleButton.MouseButton1Click:Connect(function()
	getgenv().ESP_XRay = not getgenv().ESP_XRay
	toggleButton.Text = getgenv().ESP_XRay and "❌ ปิด X-Ray ESP" or "👁️ เปิด X-Ray ESP"
end)

-- ฟังก์ชันใส่ Highlight ESP
local function addHighlight(player)
	if player == LocalPlayer then return end
	local function onChar(char)
		local hl = char:FindFirstChild("XRayHighlight")
		if hl then hl:Destroy() end

		local highlight = Instance.new("Highlight")
		highlight.Name = "XRayHighlight"
		highlight.FillColor = Color3.fromRGB(0, 255, 0)
		highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
		highlight.FillTransparency = 0.25 -- ยังเห็นทะลุ
		highlight.OutlineTransparency = 0
		highlight.Adornee = char
		highlight.Parent = char

		RunService.RenderStepped:Connect(function()
			if not highlight or not char or not char.Parent then return end
			highlight.Enabled = getgenv().ESP_XRay
		end)
	end

	if player.Character then
		onChar(player.Character)
	end
	player.CharacterAdded:Connect(onChar)
end

-- ใส่ให้ทุกคน
for _, plr in ipairs(Players:GetPlayers()) do
	addHighlight(plr)
end
Players.PlayerAdded:Connect(addHighlight)
