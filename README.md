local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("⚡ SHADOW CORE ⚡", "DarkTheme") -- หรือ Neon/Serpent/BloodTheme แล้วแต่ชอบ

local Tab = Window:NewTab("Main")
local Section = Tab:NewSection("Features")

Section:NewLabel("🎯 Welcome to SHADOW CORE")

Section:NewButton("🕶️ Toggle Invisibility", "ล่องหนแบบเท่ ๆ", function()
    print("ล่องหนแล้ว")
end)

Section:NewToggle("🚀 Flight Mode", "บินได้อย่างมีสไตล์", function(state)
    if state then
        print("บินแล้ว!")
    else
        print("เลิกบินแล้ว!")
    end
end)

Section:NewKeybind("🎮 Toggle UI", "กดปิด/เปิด UI", Enum.KeyCode.RightControl, function()
    Library:ToggleUI()
end)
