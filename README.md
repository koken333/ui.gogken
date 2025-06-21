local Lighting = game:GetService("Lighting")

-- ปิดเอฟเฟกต์เก่าที่อาจมีซ้ำ
for _, effect in pairs(Lighting:GetChildren()) do
    if effect:IsA("PostEffect") then
        effect:Destroy()
    end
end

-- Bloom (ทำให้แสงสว่างดูนุ่มนวล)
local bloom = Instance.new("BloomEffect")
bloom.Intensity = 0.3          -- ความเข้มของแสง bloom
bloom.Threshold = 1            -- ค่าแสงที่เกินจุดนี้จะ bloom
bloom.Size = 24                -- ขนาดของ bloom
bloom.Enabled = true
bloom.Parent = Lighting

-- ColorCorrection (ปรับโทนสีภาพ)
local colorCorrection = Instance.new("ColorCorrectionEffect")
colorCorrection.TintColor = Color3.fromRGB(255, 240, 220)  -- โทนสีอุ่น
colorCorrection.Contrast = 0.1
colorCorrection.Brightness = 0.05
colorCorrection.Saturation = 1.2
colorCorrection.Enabled = true
colorCorrection.Parent = Lighting

-- DepthOfField (เบลอฉากหลัง ช่วยเน้นวัตถุ)
local dof = Instance.new("DepthOfFieldEffect")
dof.FocusDistance = 50
dof.InFocusRadius = 15
dof.Enabled = true
dof.Parent = Lighting

-- SunRays (เพิ่มแสงแดดเจิดจ้า)
local sunRays = Instance.new("SunRaysEffect")
sunRays.Intensity = 0.12
sunRays.Spread = 0.15
sunRays.Enabled = true
sunRays.Parent = Lighting

print("Post-Processing Effects applied: ภาพสวยขึ้นทันที!")
