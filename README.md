----- ล็อกเป่า




local success, Library = pcall(function() 
    return loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))() 
end)

if not success or not Library then return end

local Window = Library.CreateLib("Owner Study Hub (Custom)", "BloodTheme")

-- SERVICE
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local camera = workspace.CurrentCamera
local localPlayer = Players.LocalPlayer

-- SETTINGS
_G.MyAimbotSettings = {
    AimbotEnabled = false,
    TargetPart = "Head",
    Smoothness = 0.15,
    FOV = 120,
    ESPEnabled = true,
}
local CurrentSettings = _G.MyAimbotSettings

-- DRAWING FOV (วงกลมจะโชว์ตลอดเวลา)
local fovCircle = Drawing.new("Circle")
fovCircle.Visible = true
fovCircle.Thickness = 1
fovCircle.Radius = CurrentSettings.FOV
fovCircle.Color = Color3.fromRGB(255, 0, 0)
fovCircle.Filled = false

-- ESP STORAGE & CLEANUP
local playerESPDrawings = {}

local function removeESP(userId)
    if playerESPDrawings[userId] then
        playerESPDrawings[userId]:Remove()
        playerESPDrawings[userId] = nil
    end
end

Players.PlayerRemoving:Connect(function(player)
    removeESP(player.UserId)
end)

-- ฟังก์ชันจัดการ ESP
local function updatePlayerESP(player)
    if player == localPlayer then return end
    
    if not playerESPDrawings[player.UserId] then
        local text = Drawing.new("Text")
        text.Size = 16
        text.Center = true
        text.Outline = true
        text.Visible = false
        playerESPDrawings[player.UserId] = text
    end

    local espDrawing = playerESPDrawings[player.UserId]
    local char = player.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    local hum = char and char:FindFirstChild("Humanoid")

    if CurrentSettings.ESPEnabled and hrp and hum and hum.Health > 0 then
        local pos, onscreen = camera:WorldToViewportPoint(hrp.Position)
        if onscreen then
            espDrawing.Position = Vector2.new(pos.X, pos.Y - 30)
            espDrawing.Text = string.format("[%s] %d HP", player.Name, math.floor(hum.Health))
            espDrawing.Visible = true
            espDrawing.Color = Color3.fromRGB(255, 255, 255)
        else
            espDrawing.Visible = false
        end
    else
        espDrawing.Visible = false
    end
end

-- ฟังก์ชันหาเป้าหมาย
local function getClosestTarget()
    local closestPart = nil
    local shortestDistance = CurrentSettings.FOV
    local mouseLoc = UserInputService:GetMouseLocation()

    for _, p in pairs(Players:GetPlayers()) do
        if p ~= localPlayer and p.Character then
            local part = p.Character:FindFirstChild(CurrentSettings.TargetPart)
            local hum = p.Character:FindFirstChild("Humanoid")

            if part and hum and hum.Health > 0 then
                local screenPos, onScreen = camera:WorldToViewportPoint(part.Position)
                if onScreen then
                    local dist = (Vector2.new(screenPos.X, screenPos.Y) - mouseLoc).Magnitude
                    if dist < shortestDistance then
                        shortestDistance = dist
                        closestPart = part
                    end
                end
            end
        end
    end
    return closestPart
end

-- UI SETUP
local MainTab = Window:NewTab("Main Hub")
local Combat = MainTab:NewSection("Combat Controls")
local Visuals = MainTab:NewSection("Visual Settings")

Combat:NewToggle("Enable Aimbot (Key: E)", "Toggle lock on", function(state)
    CurrentSettings.AimbotEnabled = state
end)

Combat:NewSlider("Smoothness", "Control speed", 100, 1, function(s) 
    CurrentSettings.Smoothness = s / 100 
end)

Combat:NewSlider("FOV Radius", "Size of circle", 800, 10, function(s) 
    CurrentSettings.FOV = s 
end)

Combat:NewDropdown("Target Part (Key: F)", "Select part", {"Head", "HumanoidRootPart"}, function(op)
    CurrentSettings.TargetPart = op
end)

Visuals:NewToggle("Enable Names", "Show Player Tags", function(state) 
    CurrentSettings.ESPEnabled = state 
end)

-- KEYBIND LOGIC (ตัดการแจ้งเตือนออก)
UserInputService.InputBegan:Connect(function(input, gpe)
    if gpe then return end
    
    -- กด E เพื่อเปิด/ปิดเฉพาะการล็อคเป้า
    if input.KeyCode == Enum.KeyCode.Q then
        CurrentSettings.AimbotEnabled = not CurrentSettings.AimbotEnabled
    end
    
    -- กด F เพื่อสลับจุดเล็ง
    if input.KeyCode == Enum.KeyCode.F then
        CurrentSettings.TargetPart = (CurrentSettings.TargetPart == "Head" and "HumanoidRootPart" or "Head")
    end
end)

-- MAIN LOOP
RunService.RenderStepped:Connect(function()
    -- วงกลม FOV จะแสดงตลอดเวลาและขยับตามเมาส์
    fovCircle.Position = UserInputService:GetMouseLocation()
    fovCircle.Radius = CurrentSettings.FOV
    fovCircle.Visible = true -- ให้เห็นวงกลมตลอดเพื่อวัดระยะ
    
    -- เปลี่ยนสีวงกลมให้รู้สถานะ (เขียวเมื่อเปิดใช้ / แดงเมื่อปิด)
    fovCircle.Color = CurrentSettings.AimbotEnabled and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)

    -- ระบบล็อคเป้า
    if CurrentSettings.AimbotEnabled then
        local target = getClosestTarget()
        if target then
            camera.CFrame = camera.CFrame:Lerp(CFrame.lookAt(camera.CFrame.Position, target.Position), CurrentSettings.Smoothness)
        end
    end

    -- ระบบ ESP
    for _, player in pairs(Players:GetPlayers()) do
        updatePlayerESP(player)
    end
end)
