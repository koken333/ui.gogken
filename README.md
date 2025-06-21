local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local VirtualUser = game:GetService("VirtualUser")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local Camera = workspace.CurrentCamera

-- ฟังก์ชันสร้างปุ่มสวยๆ
local function createButton(parent, name, text, posY)
    local btn = Instance.new("TextButton")
    btn.Name = name
    btn.Size = UDim2.new(0, 150, 0, 40)
    btn.Position = UDim2.new(0, 20, 0, posY)
    btn.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
    btn.BorderSizePixel = 0
    btn.TextColor3 = Color3.new(1, 1, 1)
    btn.Font = Enum.Font.GothamSemibold
    btn.TextScaled = true
    btn.Text = text
    btn.Parent = parent
    return btn
end

local function createLabel(parent, text, posY)
    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(0, 150, 0, 30)
    lbl.Position = UDim2.new(0, 20, 0, posY)
    lbl.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
    lbl.BorderSizePixel = 0
    lbl.TextColor3 = Color3.new(1, 1, 1)
    lbl.Font = Enum.Font.GothamSemibold
    lbl.TextScaled = true
    lbl.Text = text
    lbl.Parent = parent
    return lbl
end

local function createTextbox(parent, defaultText, posY)
    local tb = Instance.new("TextBox")
    tb.Size = UDim2.new(0, 150, 0, 40)
    tb.Position = UDim2.new(0, 20, 0, posY)
    tb.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
    tb.BorderSizePixel = 0
    tb.TextColor3 = Color3.new(1, 1, 1)
    tb.Font = Enum.Font.GothamSemibold
    tb.TextScaled = true
    tb.ClearTextOnFocus = false
    tb.Text = tostring(defaultText)
    tb.Parent = parent
    return tb
end

-- สร้าง GUI สำหรับ Aimbot
local aimbotGui = Instance.new("ScreenGui")
aimbotGui.Name = "Aimbot_UI"
aimbotGui.ResetOnSpawn = false
aimbotGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
aimbotGui.Parent = PlayerGui

-- ค่าตั้งต้น
getgenv().AimbotEnabled = false
local fovRadius = 100
local aimPart = "Head"

-- สร้างปุ่มเปิด/ปิด Aimbot
local toggleAimbotButton = createButton(aimbotGui, "ToggleAimbotButton", "🎯 เปิด Aimbot", 20)

-- ป้ายแสดงค่า FOV
local fovLabel = createLabel(aimbotGui, "FOV Radius: " .. fovRadius, 80)

-- กล่องใส่ค่า FOV
local fovInput = createTextbox(aimbotGui, fovRadius, 120)

-- Toggle เปิด/ปิด Aimbot
toggleAimbotButton.MouseButton1Click:Connect(function()
    getgenv().AimbotEnabled = not getgenv().AimbotEnabled
    toggleAimbotButton.Text = getgenv().AimbotEnabled and "❌ ปิด Aimbot" or "🎯 เปิด Aimbot"
end)

-- แก้ไขค่า FOV เมื่อผู้ใช้พิมพ์เสร็จ
fovInput.FocusLost:Connect(function(enterPressed)
    if enterPressed then
        local val = tonumber(fovInput.Text)
        if val and val >= 0 and val <= 500 then
            fovRadius = val
            fovLabel.Text = "FOV Radius: " .. fovRadius
        else
            fovInput.Text = tostring(fovRadius)
        end
    end
end)

-- ฟังก์ชันหาผู้เล่นที่ใกล้เป้าหมายที่สุดใน FOV
local function getClosestTarget()
    local cam = Camera
    local closest, shortest = nil, math.huge

    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer 
        and p.Character 
        and p.Character:FindFirstChild(aimPart) 
        and not LocalPlayer:IsFriendsWith(p.UserId) then -- ไม่ล็อกเพื่อน
            local pos, onScreen = cam:WorldToViewportPoint(p.Character[aimPart].Position)
            if onScreen then
                local distToCenter = (Vector2.new(pos.X, pos.Y) - Vector2.new(cam.ViewportSize.X/2, cam.ViewportSize.Y/2)).Magnitude
                if distToCenter < shortest and distToCenter < fovRadius then
                    shortest = distToCenter
                    closest = p
                end
            end
        end
    end

    return closest
end

-- สคริปต์ทำงานทุกเฟรม
RunService.RenderStepped:Connect(function()
    if getgenv().AimbotEnabled then
        local target = getClosestTarget()
        if target and target.Character and target.Character:FindFirstChild(aimPart) then
            -- หมุนกล้องล็อกเป้า
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Character[aimPart].Position)

            -- ยิงอัตโนมัติ หากยังไม่ได้กดยิง
            if not UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton1) then
                VirtualUser:Button1Down(Vector2.new(0,0))
                wait(0.05)
                VirtualUser:Button1Up(Vector2.new(0,0))
            end
        end
    end
end)
