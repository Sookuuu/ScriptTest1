-- Painel Admin básico (2 scripts)
-- Instruções:
-- 1) Coloque o ServerScript em ServerScriptService (nome: AdminServer)
-- 2) Coloque o LocalScript em StarterGui (nome: AdminClient)
-- 3) Ajuste a lista `ADMINS` com os UserIds que devem ter acesso

-- =========================
-- ServerScript: AdminServer
-- Colocar em ServerScriptService
-- =========================

-- SERVER SCRIPT START
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Players = game:GetService("Players")

-- Cria RemoteEvent se não existir
local event = ReplicatedStorage:FindFirstChild("AdminEvent")
if not event then
    event = Instance.new("RemoteEvent")
    event.Name = "AdminEvent"
    event.Parent = ReplicatedStorage
end

-- Lista de admins (UserIds). Troca pelos seus IDs.
local ADMINS = {
    -- exemplo: 12345678,
}
local adminSet = {}
for _,id in ipairs(ADMINS) do adminSet[id] = true end

local function isAdmin(player)
    return adminSet[player.UserId]
end

-- Funções admin possíveis
local actions = {}

function actions:kick(targetName, reason)
    local target = Players:FindFirstChild(targetName)
    if target then
        target:Kick(reason or "Expulso pelo admin")
        return true, "Player kickado"
    end
    return false, "Jogador não encontrado"
end

function actions:tp(sourcePlayer, targetName)
    local target = Players:FindFirstChild(targetName)
    if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") and sourcePlayer.Character and sourcePlayer.Character:FindFirstChild("HumanoidRootPart") then
        sourcePlayer.Character.HumanoidRootPart.CFrame = target.Character.HumanoidRootPart.CFrame + Vector3.new(2,0,0)
        return true, "Teleportado"
    end
    return false, "Falha ao teleportar"
end

function actions:bring(sourcePlayer, targetName)
    local target = Players:FindFirstChild(targetName)
    if target and sourcePlayer.Character and target.Character and target.Character:FindFirstChild("HumanoidRootPart") and sourcePlayer.Character:FindFirstChild("HumanoidRootPart") then
        target.Character.HumanoidRootPart.CFrame = sourcePlayer.Character.HumanoidRootPart.CFrame + Vector3.new(2,0,0)
        return true, "Trazido"
    end
    return false, "Falha ao trazer"
end

function actions:heal(targetName)
    local target = Players:FindFirstChild(targetName)
    if target and target.Character and target.Character:FindFirstChild("Humanoid") then
        target.Character.Humanoid.Health = target.Character.Humanoid.MaxHealth
        return true, "Curado"
    end
    return false, "Falha ao curar"
end

-- Handler do RemoteEvent
event.OnServerEvent:Connect(function(player, cmd, args)
    if not isAdmin(player) then
        return -- ignora se não for admin
    end

    local fn = actions[cmd]
    if fn then
        local ok, msg = pcall(function() return fn(player, table.unpack(args)) end)
        if not ok then
            warn("Erro ao executar comando admin:", msg)
        end
    else
        warn("Comando admin desconhecido:", cmd)
    end
end)
-- SERVER SCRIPT END


-- =========================
-- LocalScript: AdminClient
-- Colocar em StarterGui (LocalScript)
-- =========================

-- CLIENT SCRIPT START
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Tenta referenciar o RemoteEvent (pode ser criado pelo server)
local event = ReplicatedStorage:WaitForChild("AdminEvent")

-- Lista de nomes visuais / permissões locais (opcional)
local ADMINS_NAMES = {
    -- "Canario",
}

local function isLocalAdmin()
    for _,n in ipairs(ADMINS_NAMES) do
        if tostring(player.Name):lower() == tostring(n):lower() then return true end
    end
    return false
end

-- GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "PainelAdminGUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = playerGui

local mainFrame = Instance.new("Frame")
mainFrame.Name = "Main"
mainFrame.Size = UDim2.new(0, 420, 0, 300)
mainFrame.Position = UDim2.new(0.5, -210, 0.5, -150)
mainFrame.AnchorPoint = Vector2.new(0.5,0.5)
mainFrame.BackgroundTransparency = 0.15
mainFrame.BackgroundColor3 = Color3.fromRGB(20,20,20)
mainFrame.BorderSizePixel = 0
mainFrame.Visible = false
mainFrame.Parent = screenGui

local uiCorner = Instance.new("UICorner")
uiCorner.CornerRadius = UDim.new(0,8)
uiCorner.Parent = mainFrame

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, -20, 0, 36)
title.Position = UDim2.new(0,10,0,10)
title.BackgroundTransparency = 1
title.Text = "Painel Admin"
title.TextSize = 20
title.TextColor3 = Color3.new(1,1,1)
title.Font = Enum.Font.SourceSansBold
title.Parent = mainFrame

local closeBtn = Instance.new("TextButton")
closeBtn.Size = UDim2.new(0, 36, 0, 28)
closeBtn.Position = UDim2.new(1, -46, 0, 8)
closeBtn.Text = "X"
closeBtn.Parent = mainFrame

closeBtn.MouseButton1Click:Connect(function()
    mainFrame.Visible = false
end)

-- Command box
local cmdBox = Instance.new("TextBox")
cmdBox.Size = UDim2.new(1, -110, 0, 32)
cmdBox.Position = UDim2.new(0,10,0,56)
cmdBox.PlaceholderText = ":kick Jogador"
cmdBox.ClearTextOnFocus = false
cmdBox.Text = ""
cmdBox.Parent = mainFrame

local execBtn = Instance.new("TextButton")
execBtn.Size = UDim2.new(0, 80, 0, 32)
execBtn.Position = UDim2.new(1, -90, 0, 56)
execBtn.Text = "Executar"
execBtn.Parent = mainFrame

local output = Instance.new("TextLabel")
output.Size = UDim2.new(1, -20, 1, -120)
output.Position = UDim2.new(0,10,0,100)
output.BackgroundTransparency = 1
output.Text = ""
output.TextWrapped = true
output.TextXAlignment = Enum.TextXAlignment.Left
output.TextYAlignment = Enum.TextYAlignment.Top
output.Font = Enum.Font.SourceSans
output.TextSize = 16
output.TextColor3 = Color3.new(1,1,1)
output.Parent = mainFrame

-- Toggle com tecla (Ctrl + P)
local UserInputService = game:GetService("UserInputService")
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.P and UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
        mainFrame.Visible = not mainFrame.Visible
    end
end)

-- Ajuda rápida
local helpText = "Comandos suportados (server precisa ter o AdminServer ativo):\n:kick <Nome> - Expulsa jogador\n:tp <Nome> - Teleporta você até o jogador\n:bring <Nome> - Traz jogador até você\n:heal <Nome> - Cura jogador\n"
output.Text = helpText

local function sendCommand(raw)
    local parts = {}
    for w in string.gmatch(raw, "%S+") do table.insert(parts, w) end
    local cmd = string.gsub(parts[1] or "", ":", "")
    table.remove(parts,1)
    -- manda pro server
    event:FireServer(cmd, parts)
    output.Text = output.Text .. "\n> " .. raw
end

execBtn.MouseButton1Click:Connect(function()
    if cmdBox.Text ~= "" then
        sendCommand(cmdBox.Text)
        cmdBox.Text = ""
    end
end)

cmdBox.FocusLost:Connect(function(enterPressed)
    if enterPressed and cmdBox.Text ~= "" then
        sendCommand(cmdBox.Text)
        cmdBox.Text = ""
    end
end)

-- Mensagem inicial
if not isLocalAdmin() then
    output.Text = output.Text .. "\n\nObservação: seu usuário pode não ser admin. O servidor checa permissões antes de executar comandos sensíveis."
end

-- CLIENT SCRIPT END
