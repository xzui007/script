--[[
    Brainrot Hub PRO - ULTRA SAFE EDITION
    Script seguro e otimizado para "Roube um Brainrot" (Roblox)
    By xzui007 & Copilot
    Funções: Auto Farm, ESP, Boost, Noclip, Infinite Jump, Teleporte Altura, Auto Escape, Coleta, UI simples, Proteção anti-AFK e bugs comuns
]]

-- Roblox Services
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local VirtualUser = game:GetService("VirtualUser")
local Workspace = game:GetService("Workspace")
local TweenService = game:GetService("TweenService")

-- Aguarda PlayerGui e Character
repeat task.wait() until LocalPlayer:FindFirstChild("PlayerGui") and LocalPlayer.Character

-- Configuração
local config = {
    autoFarm = false,
    infiniteJump = false,
    noclip = false,
    espPlayers = false,
    speedBoost = false,
    tpAltura = false,
    tpDescer = false,
    autoEscape = false,
    autoCollect = false
}

local SPEED_NORMAL = 20
local SPEED_BOOST = 40

-- Proteção anti-AFK
for _,v in pairs(getconnections(LocalPlayer.Idled)) do
    v:Disable()
end
LocalPlayer.Idled:Connect(function()
    VirtualUser:Button2Down(Vector2.new())
    task.wait(1)
    VirtualUser:Button2Up(Vector2.new())
end)

-- UI simples
local gui = Instance.new("ScreenGui", LocalPlayer.PlayerGui)
gui.Name = "BrainrotUltraSafeUI"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 340, 0, 520)
frame.Position = UDim2.new(0.5, -170, 0.5, -260)
frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
frame.BackgroundTransparency = 0.05
frame.Active = true
frame.Draggable = true
Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 10)
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 38)
title.Text = "🧠 Brainrot Hub PRO ULTRA SAFE"
title.Font = Enum.Font.GothamBold
title.TextSize = 20
title.TextColor3 = Color3.new(1, 1, 1)
title.BackgroundTransparency = 1

local function criarBotao(texto, ordem, chave)
    local botao = Instance.new("TextButton")
    botao.Size = UDim2.new(0.85, 0, 0, 30)
    botao.Position = UDim2.new(0.075, 0, 0, 40 + (ordem - 1) * 38)
    botao.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    botao.TextColor3 = Color3.fromRGB(200, 255, 200)
    botao.Font = Enum.Font.GothamBold
    botao.TextSize = 16
    botao.Text = texto .. ": OFF"
    botao.Parent = frame
    Instance.new("UICorner", botao).CornerRadius = UDim.new(0, 5)
    botao.MouseButton1Click:Connect(function()
        config[chave] = not config[chave]
        botao.Text = texto .. ": " .. (config[chave] and "ON" or "OFF")
    end)
end

criarBotao("Auto Farm", 1, "autoFarm")
criarBotao("Infinite Jump", 2, "infiniteJump")
criarBotao("Noclip", 3, "noclip")
criarBotao("ESP Jogadores", 4, "espPlayers")
criarBotao("Boost Velocidade", 5, "speedBoost")
criarBotao("Teleporta Altura 1000", 6, "tpAltura")
criarBotao("Descer 100", 7, "tpDescer")
criarBotao("Auto Escape", 8, "autoEscape")
criarBotao("Coletar Drops", 9, "autoCollect")

-- ESP seguro
local function criarESP()
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") and not p.Character.Head:FindFirstChild("ESP") then
            local esp = Instance.new("BillboardGui")
            esp.Name = "ESP"
            esp.Adornee = p.Character.Head
            esp.Size = UDim2.new(0,110,0,36)
            esp.AlwaysOnTop = true
            esp.Parent = p.Character.Head
            local texto = Instance.new("TextLabel", esp)
            texto.Size = UDim2.new(1,0,1,0)
            texto.BackgroundTransparency = 1
            texto.Text = p.DisplayName
            texto.TextColor3 = Color3.new(1,0.3,0.3)
            texto.TextScaled = true
            texto.Font = Enum.Font.GothamBold
        end
    end
end

-- Auto Farm seguro
local function autoFarm()
    local char = LocalPlayer.Character
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    local closest, dist = nil, 9999
    for _, obj in ipairs(Workspace:GetDescendants()) do
        if obj:IsA("ProximityPrompt") and obj.Enabled and obj.Parent and obj.Parent:IsA("Model") then
            local pos = obj.Parent:FindFirstChild("HumanoidRootPart") or obj.Parent:FindFirstChild("Head")
            if pos then
                local d = (hrp.Position - pos.Position).Magnitude
                if d < dist then
                    dist = d
                    closest = obj
                end
            end
        end
    end
    if closest then
        local pos = closest.Parent:FindFirstChild("HumanoidRootPart") or closest.Parent:FindFirstChild("Head")
        if pos then
            TweenService:Create(hrp, TweenInfo.new(0.4, Enum.EasingStyle.Linear), {CFrame = CFrame.new(pos.Position + Vector3.new(0,2,0))}):Play()
            task.wait(0.45)
            pcall(function() fireproximityprompt(closest, 1) end)
        end
    end
end

-- Teleportar para Y = 1000
local function tpAltura1000()
    local char = LocalPlayer.Character
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    local pos = hrp.Position
    hrp.CFrame = CFrame.new(pos.X, 1000, pos.Z)
end

-- Descer 100
local function descer100()
    local char = LocalPlayer.Character
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    local pos = hrp.Position
    hrp.CFrame = CFrame.new(pos.X, pos.Y - 100, pos.Z)
end

-- Auto Escape seguro
local function autoEscape()
    local char = LocalPlayer.Character
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    for _,v in ipairs(Workspace:GetDescendants()) do
        if v:IsA("BasePart") and (v.Name:lower():find("escape") or v.Name:lower():find("saida")) then
            TweenService:Create(hrp, TweenInfo.new(0.7, Enum.EasingStyle.Linear), {CFrame = CFrame.new(v.Position + Vector3.new(0,5,0))}):Play()
            break
        end
    end
end

-- Auto Collect seguro
local function autoCollect()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    for _,drop in ipairs(Workspace:GetDescendants()) do
        if drop:IsA("BasePart") and (drop.Name:lower():find("drop") or drop.Name:lower():find("item") or drop.Name:lower():find("reward")) then
            TweenService:Create(hrp, TweenInfo.new(0.4, Enum.EasingStyle.Linear), {CFrame = CFrame.new(drop.Position + Vector3.new(0,3,0))}):Play()
            task.wait(0.15)
        end
    end
end

-- Garante WalkSpeed ao respawnar
LocalPlayer.CharacterAdded:Connect(function(char)
    char:WaitForChild("Humanoid").WalkSpeed = SPEED_NORMAL
end)

-- Loop principal
RunService.RenderStepped:Connect(function()
    local char = LocalPlayer.Character
    local humanoid = char and char:FindFirstChildOfClass("Humanoid")
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if humanoid then
        humanoid.WalkSpeed = config.speedBoost and SPEED_BOOST or SPEED_NORMAL
    end
    if config.noclip and humanoid then
        humanoid:ChangeState(11)
    end
    if config.espPlayers then
        criarESP()
    end
    if config.tpAltura then
        tpAltura1000()
        config.tpAltura = false -- só executa uma vez por clique
    end
    if config.tpDescer then
        descer100()
        config.tpDescer = false -- só executa uma vez por clique
    end
    if config.autoFarm then
        autoFarm()
    end
    if config.autoEscape then
        autoEscape()
    end
    if config.autoCollect then
        autoCollect()
    end
end)

-- Hotkeys e Infinite Jump
UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.KeyCode == Enum.KeyCode.Space and config.infiniteJump then
        local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildWhichIsA("Humanoid")
        if hum then hum:ChangeState(Enum.HumanoidStateType.Jumping) end
    end
    if input.KeyCode == Enum.KeyCode.Q then
        config.speedBoost = not config.speedBoost
    end
    if input.KeyCode == Enum.KeyCode.G then
        config.tpAltura = true
    end
    if input.KeyCode == Enum.KeyCode.H then
        config.tpDescer = true
    end
    if input.KeyCode == Enum.KeyCode.E then
        config.autoEscape = not config.autoEscape
    end
    if input.KeyCode == Enum.KeyCode.Z then
        config.autoCollect = not config.autoCollect
    end
end)

print("✅ Brainrot Hub PRO ULTRA SAFE carregado com sucesso! Teleporte Altura (G), Descer 100 (H). Aproveite.")
