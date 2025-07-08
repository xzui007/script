--[[
    Brainrot Hub PRO - Edição Turbo ADVANCED
    Script avançado para "Roube um Brainrot"
    Recursos extras, segurança e automação máxima
    Atualizado por Copilot | https://github.com/xzui007
]]

-- Serviços Roblox
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local VirtualUser = game:GetService("VirtualUser")
local Workspace = game:GetService("Workspace")
local TweenService = game:GetService("TweenService")

repeat task.wait() until LocalPlayer and LocalPlayer:FindFirstChild("PlayerGui") and LocalPlayer.Character

-- Configuração
local config = {
    autoFarm = false,
    infiniteJump = false,
    noclip = false,
    teleport = false,
    alertaProximidade = false,
    randomTP = false,
    espPlayers = false,
    speedBoost = false,
    floatToBase = false,
    autoEscape = false,
    autoCollectDrops = false
}

-- WalkSpeed
local SPEED_NORMAL = 20
local SPEED_BOOST = 40

-- Proteção anti-kick/idle
for _,v in pairs(getconnections(LocalPlayer.Idled)) do v:Disable() end
LocalPlayer.Idled:Connect(function()
    VirtualUser:Button2Down(Vector2.new())
    task.wait(1)
    VirtualUser:Button2Up(Vector2.new())
end)

-- UI
local gui = Instance.new("ScreenGui", LocalPlayer.PlayerGui)
gui.Name = "BrainrotHubUI"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 400, 0, 650)
frame.Position = UDim2.new(0.5, -200, 0.5, -325)
frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
frame.BackgroundTransparency = 0.07
frame.Active = true
frame.Draggable = true
Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 12)

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 40)
title.Text = "🧠 Brainrot Hub PRO Turbo ADVANCED"
title.Font = Enum.Font.GothamBold
title.TextSize = 23
title.TextColor3 = Color3.new(1, 1, 1)
title.BackgroundTransparency = 1

local info = Instance.new("TextLabel", frame)
info.Size = UDim2.new(1, 0, 0, 22)
info.Position = UDim2.new(0, 0, 1, -30)
info.Text = "F = Teleport | T = Random TP | Q = Boost | V = Float | E = Escape | Z = Drops"
info.TextColor3 = Color3.fromRGB(200,255,120)
info.Font = Enum.Font.Gotham
info.TextSize = 13
info.BackgroundTransparency = 1

-- Teleporte seguro via Tween
local function safeTweenTo(targetPos, speed)
    local char = LocalPlayer.Character
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    local tween = TweenService:Create(hrp, TweenInfo.new(speed or 0.7, Enum.EasingStyle.Linear), {CFrame = CFrame.new(targetPos)})
    tween:Play()
    tween.Completed:Wait()
end

-- Auto Escape (Procura partes chamadas "Escape" ou "Saída")
local function autoEscape()
    local char = LocalPlayer.Character
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    for _,v in ipairs(Workspace:GetDescendants()) do
        if v:IsA("BasePart") and (v.Name:lower():find("escape") or v.Name:lower():find("saida")) then
            safeTweenTo(v.Position + Vector3.new(0,4,0), 1)
            break
        end
    end
end

-- Auto Collect (Procura partes com "drop", "item" ou "reward")
local function autoCollectDrops()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    for _,drop in ipairs(Workspace:GetDescendants()) do
        if drop:IsA("BasePart") and (drop.Name:lower():find("drop") or drop.Name:lower():find("item") or drop.Name:lower():find("reward")) then
            safeTweenTo(drop.Position + Vector3.new(0,2,0), 0.5)
            task.wait(0.1)
        end
    end
end

-- Alerta de proximidade
local function checarProximidade(distMax)
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    for _,p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            local dist = (hrp.Position - p.Character.HumanoidRootPart.Position).Magnitude
            if dist < distMax then
                if not gui:FindFirstChild("ProxAlert") then
                    local alert = Instance.new("TextLabel", gui)
                    alert.Name = "ProxAlert"
                    alert.Size = UDim2.new(1,0,0,32)
                    alert.Position = UDim2.new(0,0,0,0)
                    alert.BackgroundTransparency = 0.3
                    alert.BackgroundColor3 = Color3.fromRGB(200, 40, 40)
                    alert.Text = "🚨 Jogador próximo! ["..math.floor(dist).." studs]"
                    alert.Font = Enum.Font.GothamBold
                    alert.TextSize = 19
                    alert.TextColor3 = Color3.new(1,1,1)
                    task.spawn(function()
                        task.wait(2.5)
                        alert:Destroy()
                    end)
                end
                break
            end
        end
    end
end

-- ESP Jogadores (corrigido)
local function criarESP()
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") and not p.Character.Head:FindFirstChild("ESP") then
            local esp = Instance.new("BillboardGui")
            esp.Name = "ESP"
            esp.Size = UDim2.new(0,100,0,40)
            esp.AlwaysOnTop = true
            esp.Parent = p.Character.Head
            local texto = Instance.new("TextLabel", esp)
            texto.Size = UDim2.new(1,0,1,0)
            texto.BackgroundTransparency = 1
            texto.Text = p.DisplayName
            texto.TextColor3 = Color3.new(1,0,0)
            texto.TextScaled = true
            texto.Font = Enum.Font.GothamBold
        end
    end
end

-- Auto Farm (corrigido)
local function autoFarm()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    local minDist, objAlvo = math.huge, nil
    for _, obj in ipairs(Workspace:GetDescendants()) do
        if obj:IsA("ProximityPrompt") and obj.Parent and obj.Parent:IsA("Model") then
            local model = obj.Parent
            local pos = model:FindFirstChild("HumanoidRootPart") or model:FindFirstChild("Head")
            if pos then
                local dist = (hrp.Position - pos.Position).Magnitude
                if dist < minDist then
                    minDist = dist
                    objAlvo = obj
                end
            end
        end
    end
    if objAlvo then
        local pos = objAlvo.Parent:FindFirstChild("HumanoidRootPart") or objAlvo.Parent:FindFirstChild("Head")
        if pos then
            safeTweenTo(pos.Position + Vector3.new(0,2,0), 0.5)
            fireproximityprompt(objAlvo, 1)
        end
    end
end

-- Garante que WalkSpeed seja restaurado no respawn
LocalPlayer.CharacterAdded:Connect(function(char)
    char:WaitForChild("Humanoid").WalkSpeed = SPEED_NORMAL
end)

-- Loop principal
RunService.RenderStepped:Connect(function()
    local char = LocalPlayer.Character
    local humanoid = char and char:FindFirstChildOfClass("Humanoid")
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    -- Boost de velocidade via WalkSpeed seguro
    if humanoid then
        if config.speedBoost then
            humanoid.WalkSpeed = SPEED_BOOST
        else
            humanoid.WalkSpeed = SPEED_NORMAL
        end
    end
    if config.noclip and char and humanoid then
        humanoid:ChangeState(11)
    end
    if config.floatToBase and hrp then
        local goal = Vector3.new(0, 120, 0)
        hrp.CFrame = hrp.CFrame:Lerp(CFrame.new(goal), 0.05)
    end
    if config.autoFarm then
        autoFarm()
    end
    if config.alertaProximidade then
        checarProximidade(18)
    end
    if config.autoEscape then
        autoEscape()
    end
    if config.autoCollectDrops then
        autoCollectDrops()
    end
end)

UserInputService.InputBegan:Connect(function(input, gp)
    if gp then return end
    if input.KeyCode == Enum.KeyCode.Space and config.infiniteJump then
        local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildWhichIsA("Humanoid")
        if hum then hum:ChangeState(Enum.HumanoidStateType.Jumping) end
    end
    if input.KeyCode == Enum.KeyCode.T then
        local root = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if root then
            root.CFrame = CFrame.new(math.random(-500,500), 50, math.random(-500,500))
        end
    end
    if input.KeyCode == Enum.KeyCode.Q then
        config.speedBoost = not config.speedBoost
    end
    if input.KeyCode == Enum.KeyCode.V then
        config.floatToBase = not config.floatToBase
    end
    if input.KeyCode == Enum.KeyCode.C then
        local root = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
        if root then
            root.CFrame = root.CFrame + Vector3.new(0, 1000, 0)
        end
    end
    if input.KeyCode == Enum.KeyCode.F then
        local list = {}
        for _,p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                table.insert(list, p.Character.HumanoidRootPart.Position)
            end
        end
        if #list > 0 then
            local pos = list[math.random(1,#list)]
            safeTweenTo(pos + Vector3.new(0,2,0), 0.8)
        end
    end
    if input.KeyCode == Enum.KeyCode.E then
        config.autoEscape = not config.autoEscape
    end
    if input.KeyCode == Enum.KeyCode.Z then
        config.autoCollectDrops = not config.autoCollectDrops
    end
end)

RunService.RenderStepped:Connect(function()
    if config.espPlayers then criarESP() end
end)

-- Botões UI
local function criarBotao(texto, ordem, chave)
    local botao = Instance.new("TextButton")
    botao.Size = UDim2.new(0.9, 0, 0, 34)
    botao.Position = UDim2.new(0.05, 0, 0, 48 + (ordem - 1) * 45)
    botao.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    botao.TextColor3 = Color3.fromRGB(255, 255, 255)
    botao.Font = Enum.Font.Gotham
    botao.TextSize = 17
    botao.Text = texto .. ": OFF"
    botao.Parent = frame
    Instance.new("UICorner", botao).CornerRadius = UDim.new(0, 6)

    botao.MouseButton1Click:Connect(function()
        config[chave] = not config[chave]
        botao.Text = texto .. ": " .. (config[chave] and "ON" or "OFF")
    end)
end

criarBotao("✅ Auto Farm", 1, "autoFarm")
criarBotao("🌀 Infinite Jump", 2, "infiniteJump")
criarBotao("🚪 Noclip", 3, "noclip")
criarBotao("🛸 Teleport (C)", 4, "teleport")
criarBotao("🔊 Alerta Proximidade", 5, "alertaProximidade")
criarBotao("👁️ ESP Jogadores", 6, "espPlayers")
criarBotao("💨 Boost Velocidade (Q)", 7, "speedBoost")
criarBotao("🚀 Flutuar até Base (V)", 8, "floatToBase")
criarBotao("🆘 Auto Escape (E)", 9, "autoEscape")
criarBotao("💎 Coletar Drops (Z)", 10, "autoCollectDrops")

print("✅ Brainrot Hub Turbo ADVANCED recarregado com funções corrigidas!")

-- Fim do Script
