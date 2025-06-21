local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")
local itemName = "Slap"

-- ค้นหาและคลิกปุ่มซื้อใน GUI
local function clickBuyButton()
    for _, gui in ipairs(PlayerGui:GetDescendants()) do
        if gui:IsA("TextButton") or gui:IsA("ImageButton") then
            if gui.Text and gui.Text:lower():find(itemName:lower()) then
                gui:Invoke()               -- สำหรับปุ่มแบบ remote function
                -- ถ้าปุ่มใช้ MouseButton1Click:
                spawn(function() gui:MouseButton1Click() end)
                print("กดซื้อไอเทม:", gui.Text)
                return true
            end
        end
    end
    warn("ไม่พบปุ่มซื้อไอเทม Slap ใน GUI")
    return false
end

-- เรียกใช้
clickBuyButton()
