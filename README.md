# Fast-attack
Blox fruits script
-- // EXX Premium Hub - Versão Extrema (Fast Attack 0.005s + FPS Booster Hardcore)
local Players = game:GetService("Players")
local Workspace = game:GetService("Workspace")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer

-- // 1. FPS BOOSTER HARDCORE (Desativação total de efeitos gráficos)
local function applyHardcoreFPSBoost()
    settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
    Workspace.StreamingEnabled = false
    
    for _, v in ipairs(Workspace:GetDescendants()) do
        if v:IsA("BasePart") then
            v.Material = Enum.Material.SmoothPlastic
            v.Reflectance = 0
            v.CastShadow = false
        elseif v:IsA("ParticleEmitter") or v:IsA("Trail") or v:IsA("Fire") or v:IsA("Smoke") or v:IsA("Explosion") then
            v:Destroy() -- Remove completamente em vez de apenas desativar para limpar a memória
        end
    end

    Lighting.GlobalShadows = false
    Lighting.Brightness = 2
    Lighting.FogEnd = 9e9
    
    for _, effect in ipairs(Lighting:GetChildren()) do
        if effect:IsA("PostEffect") or effect:IsA("Atmosphere") or effect:IsA("Sky") then
            effect:Destroy()
        end
    end
end

pcall(applyHardcoreFPSBoost)

-- // 2. FAST ATTACK ULTRA-AGRESSIVO (0.005s)
local CombatFramework = require(LocalPlayer.PlayerScripts.CombatFramework)
local Localplr = CombatFramework.activeController

local function getNearestEnemy()
    local target, shortestDistance = nil, math.huge
    local character = LocalPlayer.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") then return nil end
    
    local rootPos = character.HumanoidRootPart.Position
    for _, enemy in ipairs(Workspace.Enemies:GetChildren()) do
        local hrp = enemy:FindFirstChild("HumanoidRootPart")
        local hum = enemy:FindFirstChild("Humanoid")
        if hrp and hum and hum.Health > 0 then
            local dist = (hrp.Position - rootPos).Magnitude
            if dist < shortestDistance and dist <= 350 then
                shortestDistance = dist
                target = enemy
            end
        end
    end
    return target
end

task.spawn(function()
    while true do
        pcall(function()
            if Localplr and Localplr.active then
                local targetEnemy = getNearestEnemy()
                if targetEnemy and targetEnemy:FindFirstChild("HumanoidRootPart") then
                    Localplr.active.attacker:hitServerCode()
                    Localplr.active.weapon:attack()
                end
            end
        end)
        task.wait(0.005)
    end
end)
