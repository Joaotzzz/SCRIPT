local Players = game:GetService("Players")
local player = Players.LocalPlayer
local backpack = player:WaitForChild("Backpack")

-- Criar Tool
local tool = Instance.new("Tool")
tool.Name = "HitKill Bastão"
tool.RequiresHandle = true

-- Criar Handle
local handle = Instance.new("Part")
handle.Name = "Handle"
handle.Size = Vector3.new(1,4,1)
handle.Color = Color3.fromRGB(255,0,0)
handle.Anchored = false
handle.CanCollide = true
handle.Parent = tool

-- Criar Partículas
local particle = Instance.new("ParticleEmitter")
particle.Color = ColorSequence.new(Color3.new(1,0,0))
particle.Rate = 20
particle.Parent = handle

-- Criar Som
local sound = Instance.new("Sound")
sound.SoundId = "rbxassetid://12222005"
sound.Volume = 1
sound.Parent = tool

-- Função para criar explosão visual e som
local function createExplosion(position)
    -- Explosão visual com ParticleEmitter
    local explosionPart = Instance.new("Part")
    explosionPart.Size = Vector3.new(1,1,1)
    explosionPart.Anchored = true
    explosionPart.CanCollide = false
    explosionPart.Transparency = 1
    explosionPart.CFrame = CFrame.new(position)
    explosionPart.Parent = workspace

    local explosionParticles = Instance.new("ParticleEmitter")
    explosionParticles.Texture = "rbxassetid://243660364" -- efeito de explosão
    explosionParticles.Speed = NumberRange.new(5,10)
    explosionParticles.Lifetime = NumberRange.new(0.5,1)
    explosionParticles.Rate = 100
    explosionParticles.Parent = explosionPart

    -- Som de explosão
    local explosionSound = Instance.new("Sound")
    explosionSound.SoundId = "rbxassetid://138186576" -- som de explosão
    explosionSound.Volume = 1
    explosionSound.Parent = explosionPart
    explosionSound:Play()

    -- Remove após 2 segundos
    game.Debris:AddItem(explosionPart, 2)
end

-- Script de Ragdoll + Hit Kill
handle.Touched:Connect(function(hit)
    local character = hit.Parent
    local humanoid = character and character:FindFirstChildOfClass("Humanoid")

    if humanoid and character ~= player.Character then
        humanoid.Health = 0

        humanoid.Died:Connect(function()
            -- Cria explosão no torso do personagem
            local torso = character:FindFirstChild("HumanoidRootPart") or character:FindFirstChild("Torso")
            if torso then
                createExplosion(torso.Position)
            end

            for _, part in ipairs(character:GetDescendants()) do
                if part:IsA("Motor6D") then
                    part:Destroy()
                elseif part:IsA("BasePart") then
                    part.CanCollide = true
                    part.Anchored = false
                end
            end
        end)
    end
end)

tool.Activated:Connect(function()
    sound:Play()
end)

-- Interface
local gui = Instance.new("ScreenGui")
gui.Name = "HitKillUI"
gui.ResetOnSpawn = false

local label = Instance.new("TextLabel")
label.Parent = gui
label.Size = UDim2.new(0.3, 0, 0.05, 0)
label.Position = UDim2.new(0.35, 0, 0.9, 0)
label.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
label.TextColor3 = Color3.fromRGB(255, 0, 0)
label.TextScaled = true
label.Text = "🪄 Bastão Hit Kill equipado!"
label.Visible = false

gui.Parent = player:WaitForChild("PlayerGui")

tool.Equipped:Connect(function()
    label.Visible = true
end)

tool.Unequipped:Connect(function()
    label.Visible = false
end)

-- Coloca a Tool na sua mochila
tool.Parent = backpack
