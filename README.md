--// RTX AUTO — Real Light Simulation by ChatGPT
local Lighting = game:GetService("Lighting")
local RunService = game:GetService("RunService")

-- Resetar efeitos antigos
for _, v in ipairs(Lighting:GetChildren()) do
    if v:IsA("PostEffect") then
        v:Destroy()
    end
end

----------------------------------------------------------------
-- EFEITOS PRINCIPAIS
----------------------------------------------------------------

local function addEffects()
    local bloom = Instance.new("BloomEffect")
    bloom.Intensity = 1.0
    bloom.Threshold = 0.85
    bloom.Size = 40
    bloom.Parent = Lighting

    local cc = Instance.new("ColorCorrectionEffect")
    cc.Brightness = 0.08
    cc.Contrast = 0.35
    cc.Saturation = 0.35
    cc.TintColor = Color3.fromRGB(255, 250, 235)
    cc.Parent = Lighting

    local sr = Instance.new("SunRaysEffect")
    sr.Intensity = 0.12
    sr.Spread = 1
    sr.Parent = Lighting
end

addEffects()

----------------------------------------------------------------
-- CONFIGURAÇÕES DIA/NOITE
----------------------------------------------------------------

local function applyDaySettings()
    Lighting.Brightness = 2.8               -- brilho reduzido (para não estourar)
    Lighting.GlobalShadows = true
    Lighting.Ambient = Color3.fromRGB(180, 180, 180)
    Lighting.OutdoorAmbient = Color3.fromRGB(210, 210, 210)
    Lighting.ShadowSoftness = 0.35         -- sombra suave
end

local function applyNightSettings()
    Lighting.Brightness = 1.3               -- mais escuro geral
    Lighting.GlobalShadows = true
    Lighting.Ambient = Color3.fromRGB(20, 20, 45)   -- áreas sem luz ficam escuras
    Lighting.OutdoorAmbient = Color3.fromRGB(50, 50, 80)
    Lighting.ShadowSoftness = 0.2           -- sombra mais dura para parecer real
end

local function applyLampBoost()
    -- DEIXA LUZES MAIS FORTES À NOITE
    Lighting.ColorShift_Bottom = Color3.fromRGB(50, 50, 80)
    Lighting.ColorShift_Top = Color3.fromRGB(80, 80, 110)
end

local function removeLampBoost()
    Lighting.ColorShift_Bottom = Color3.fromRGB(0, 0, 0)
    Lighting.ColorShift_Top = Color3.fromRGB(0, 0, 0)
end

----------------------------------------------------------------
-- DETECÇÃO AUTOMÁTICA DO HORÁRIO
-- Atualiza em tempo real sem travar FPS
----------------------------------------------------------------

RunService.Heartbeat:Connect(function()
    local time = Lighting.ClockTime

    -- Período que consideramos noite
    local isNight = (time >= 18 or time < 6)

    if isNight then
        applyNightSettings()
        applyLampBoost()
    else
        applyDaySettings()
        removeLampBoost()
    end
end)
