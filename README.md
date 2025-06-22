local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- ฟังก์ชันสร้าง UI สวยๆ
local function createButton(parent, name, text, posY)
    local btn = Instance.new("TextButton")
    btn.Name = name
    btn.Size = UDim2.new(0, 150, 0, 40)
    btn.Position = UDim2.new(0, 20, 0, posY)
    btn.BackgroundColor3 = Color3.fromRGB(40, 40, 50)
    btn.BorderSizePixel = 0
    btn.TextColor3 = Color3.fromRGB(1,1,1)
    btn.Font = Enum.Font.GothamBold
    btn.TextScaled = true
    btn.Text = text
    btn.Parent = parent
    return btn
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
                local dist = (character.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
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
Players.PlayerRemoving:Connect(function(p)
    local tag = p.Character and p.Character:FindFirstChild("ESP_Tag")
    if tag then tag:Destroy() end
end)

-- ====== TOGGLE UI VISIBILITY (พับ/แสดงเมนู) ======
local mainToggleGui = Instance.new("ScreenGui", PlayerGui)
mainToggleGui.Name = "MainToggle_UI"
mainToggleGui.ResetOnSpawn = false
mainToggleGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

local isMinimized = false
local toggleMainButton = Instance.new("TextButton")
toggleMainButton.Size = UDim2.new(0, 150, 0, 40)
toggleMainButton.Position = UDim2.new(0, 180, 0, 20)
toggleMainButton.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
toggleMainButton.TextColor3 = Color3.fromRGB(1,1,1)
toggleMainButton.Font = Enum.Font.GothamBold
toggleMainButton.TextScaled = true
toggleMainButton.Text = "🔽 พับเมนู"
toggleMainButton.Parent = mainToggleGui
toggleMainButton.Active = true
toggleMainButton.Draggable = true

toggleMainButton.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    toggleMainButton.Text = isMinimized and "🔼 แสดงเมนู" or "🔽 พับเมนู"

    -- ซ่อน/แสดง ESP UI
    espGui.Enabled = not isMinimized
end)

