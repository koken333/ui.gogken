local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/xHeptc/Kavo-UI-Library/main/source.lua"))()
local Window = Library.CreateLib("Gohken",    "Ocean")

local Tab = Window:NewTab("tereport")
local Section = Tab:NewSection("Section Name")
Section:NewButton("tereport", "กดดิสัส", function()
    print("Clicked")
end)

