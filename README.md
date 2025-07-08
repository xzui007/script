--[[
    Brainrot Hub - Versão Inspirada no Youtube
    Para "Roube um Brainrot" Roblox
    Funções: AutoFarm, AutoCollect, Noclip, ESP, Fly, Boost, Float, UI moderna
    github.com/xzui007
]]

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local VirtualUser = game:GetService("VirtualUser")
local Workspace = game:GetService("Workspace")
local TweenService = game:GetService("TweenService")

repeat task.wait() until LocalPlayer and LocalPlayer:FindFirstChild("PlayerGui") and LocalPlayer.Character

local config = {
    autoFarm = false,
    autoCollect = false,
    infiniteJump = false,
    noclip = false,
    esp = false,
    fly = false,
    boost = false,
    float = false
}

-- Anti AFK
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
frame.Size = UDim2.new(0, 350, 0, 470)
frame.Position = UDim2.new(0.5, -175, 0.5, -235)
frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
frame.BackgroundTransparency = 0.07
frame.Active = true
frame.Draggable = true
Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 12)

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 40)
title.Text = "🧠 Brainrot Hub - Youtube Style"
title.Font = Enum.Font.GothamBold
title.TextSize = 21
title.TextColor3 = Color3.new(1, 1, 1)
title.BackgroundTransparency = 1

local info = Instance.new("TextLabel", frame)
info.Size = UDim2.new(1, 0, 0, 20)
info.Position = UDim2.new(0, 0, 1, -28)
info.Text = "Hotkeys: T=Random TP | Q=Boost | F=Fly | V=Float"
info.TextColor3 = Color3.fromRGB(255,255,150)
info.Font = Enum.Font.Gotham
info.TextSize = 12
info.BackgroundTransparency = 1

-- AutoFarm e AutoCollect
local function getClosestPrompt()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return nil end
    local minDist, objAlvo = math.huge, nil
    for _, obj in pairs(Workspace:GetDescendants()) do
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
    return objAlvo
end

local function autoCollectDrops()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    for _,drop in pairs(Workspace:GetDescendants()) do
        if drop:IsA("Part") and drop.Name:lower():find("drop") then
            hrp.CFrame = CFrame.new(drop.Position + Vector3.new(0,1.5,0))
            task.wait(0.1)
        end
    end
end

-- ESP
local function criarESP()
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") and not p.Character.Head:FindFirstChild("ESP") then
            local esp = Instance.new("BillboardGui", p.Character.Head)
            esp.Name = "ESP"
            esp.Size = UDim2.new(0,100,0,40)
            esp.AlwaysOnTop = true
            local texto = Instance.new("TextLabel", esp)
            texto.Size = UDim2.new(1,0,1,0)
            texto.BackgroundTransparency = 1
            texto.Text = p.DisplayName
            texto.TextColor3 = Color3.new(1,0,0)
            texto.TextScaled = true
        end
    end
end

-- Loop de funções
RunService.RenderStepped:Connect(function()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if config.noclip and char and char:FindFirstChildOfClass("Humanoid") then
        char:FindFirstChildOfClass("Humanoid"):ChangeState(11)
    end
    if config.boost and hrp then
        hrp.CFrame = hrp.CFrame + hrp.CFrame.LookVector * 2.2
    end
    if config.float and hrp then
        local goal = Vector3.new(0, 120, 0)
        hrp.CFrame = hrp.CFrame:Lerp(CFrame.new(goal), 0.05)
    end
    if config.autoFarm and hrp then
        local obj = getClosestPrompt()
        if obj then
            local pos = obj.Parent:FindFirstChild("HumanoidRootPart") or obj.Parent:FindFirstChild("Head")
            if pos then
                hrp.CFrame = CFrame.new(pos.Position + Vector3.new(0,2,0))
                fireproximityprompt(obj, 0)
            end
        end
    end
    if config.autoCollect then
        autoCollectDrops()
    end
    if config.esp then
        criarESP()
    end
    if config.fly and hrp then
        hrp.Velocity = Vector3.new(0, 35, 0)
    end
end)

-- Hotkeys
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
        config.boost = not config.boost
    end
    if input.KeyCode == Enum.KeyCode.F then
        config.fly = not config.fly
    end
    if input.KeyCode == Enum.KeyCode.V then
        config.float = not config.float
    end
end)

-- Botões UI
local function criarBotao(texto, ordem, chave)
    local botao = Instance.new("TextButton")
    botao.Size = UDim2.new(0.9, 0, 0, 34)
    botao.Position = UDim2.new(0.05, 0, 0, 48 + (ordem - 1) * 45)
    botao.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
    botao.TextColor3 = Color3.fromRGB(255, 255, 255)
    botao.Font = Enum.Font.Gotham
    botao.TextSize = 16
    botao.Text = texto .. ": OFF"
    botao.Parent = frame
    Instance.new("UICorner", botao).CornerRadius = UDim.new(0, 6)

    botao.MouseButton1Click:Connect(function()
        config[chave] = not config[chave]
        botao.Text = texto .. ": " .. (config[chave] and "ON" or "OFF")
    end)
end

criarBotao("✅ Auto Farm", 1, "autoFarm")
criarBotao("💎 Auto Collect", 2, "autoCollect")
criarBotao("🌀 Infinite Jump", 3, "infiniteJump")
criarBotao("🚪 Noclip", 4, "noclip")
criarBotao("👁️ ESP", 5, "esp")
criarBotao("🛸 Fly (F)", 6, "fly")
criarBotao("💨 Boost (Q)", 7, "boost")
criarBotao("🚀 Float (V)", 8, "float")

print("✅ Brainrot Hub - Youtube Style carregado com sucesso!")
