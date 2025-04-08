-- BY ISAAC | Flee the Facility Script GUI
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local UIS = game:GetService("UserInputService")
local replicated = game:GetService("ReplicatedStorage")
local camera = workspace.CurrentCamera

-- Notificação de boas-vindas
game.StarterGui:SetCore("SendNotification", {
	Title = "BY ISAAC",
	Text = "Você recebeu um emblema!",
	Duration = 4
})
wait(4)
game.StarterGui:SetCore("SendNotification", {
	Title = "Agradecimento",
	Text = "Obrigado por usar, " .. player.Name .. "!",
	Duration = 5
})
-- GUI principal
local player = game.Players.LocalPlayer
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
ScreenGui.Name = "IsaacFleePanel"

local main = Instance.new("Frame", ScreenGui)
main.Size = UDim2.new(0, 300, 0, 400)
main.Position = UDim2.new(0.05, 0, 0.2, 0)
main.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
main.Active = true
main.Draggable = true
main.BorderSizePixel = 0

-- Botão de minimizar
local minimize = Instance.new("TextButton", main)
minimize.Size = UDim2.new(0, 30, 0, 30)
minimize.Position = UDim2.new(1, -35, 0, 5)
minimize.Text = "-"
minimize.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
minimize.TextColor3 = Color3.new(1,1,1)

local contentVisible = true
minimize.MouseButton1Click:Connect(function()
    contentVisible = not contentVisible
    for _, v in pairs(main:GetChildren()) do
        if v ~= minimize then
            v.Visible = contentVisible
        end
    end
end)

-- Função de botão com círculo indicador
local function createToggleButton(parent, name, pos, callback)
    local btn = Instance.new("TextButton", parent)
    btn.Size = UDim2.new(0, 200, 0, 30)
    btn.Position = pos
    btn.Text = name
    btn.BackgroundColor3 = Color3.fromRGB(60,60,60)
    btn.TextColor3 = Color3.new(1,1,1)

    local circle = Instance.new("Frame", btn)
    circle.Size = UDim2.new(0, 20, 0, 20)
    circle.Position = UDim2.new(1, -25, 0.5, -10)
    circle.BackgroundColor3 = Color3.fromRGB(100,100,100)
    circle.BorderSizePixel = 0
    circle.Name = "Status"

    local active = false
    btn.MouseButton1Click:Connect(function()
        active = not active
        circle.BackgroundColor3 = active and Color3.fromRGB(0,255,0) or Color3.fromRGB(255,0,0)
        callback(active)
    end)
end

-- AUTO MARRETA
createToggleButton(main, "AUTO MARRETA", UDim2.new(0, 10, 0, 40), function(state)
    print("Auto marreta " .. (state and "ativado" or "desativado"))
end)

-- Área de velocidade
local speedLabel = Instance.new("TextLabel", main)
speedLabel.Size = UDim2.new(0, 200, 0, 20)
speedLabel.Position = UDim2.new(0, 10, 0, 80)
speedLabel.Text = "Velocidade: 16"
speedLabel.TextColor3 = Color3.new(1,1,1)
speedLabel.BackgroundTransparency = 1

local speed = 16
local function updateSpeed(newSpeed)
    speed = newSpeed
    speedLabel.Text = "Velocidade: " .. speed
    if player.Character and player.Character:FindFirstChild("Humanoid") then
        player.Character.Humanoid.WalkSpeed = speed
    end
end

local btnMenos = Instance.new("TextButton", main)
btnMenos.Size = UDim2.new(0, 40, 0, 25)
btnMenos.Position = UDim2.new(0, 10, 0, 105)
btnMenos.Text = "-"
btnMenos.BackgroundColor3 = Color3.fromRGB(80,80,80)
btnMenos.TextColor3 = Color3.new(1,1,1)

local btnMais = Instance.new("TextButton", main)
btnMais.Size = UDim2.new(0, 40, 0, 25)
btnMais.Position = UDim2.new(0, 60, 0, 105)
btnMais.Text = "+VEL"
btnMais.BackgroundColor3 = Color3.fromRGB(80,80,80)
btnMais.TextColor3 = Color3.new(1,1,1)

btnMais.MouseButton1Click:Connect(function()
    updateSpeed(speed + 4)
end)

btnMenos.MouseButton1Click:Connect(function()
    updateSpeed(math.max(8, speed - 4))
end)

-- Noclip
local noclip
createToggleButton(main, "Noclip", UDim2.new(0, 10, 0, 140), function(state)
    if state then
        noclip = game:GetService("RunService").Stepped:Connect(function()
            local char = player.Character
            if char then
                for _, v in pairs(char:GetDescendants()) do
                    if v:IsA("BasePart") then
                        v.CanCollide = false
                    end
                end
            end
        end)
    else
        if noclip then
            noclip:Disconnect()
        end
    end
end)

-- Detectar computadores
local computadores = {}
for _,v in pairs(workspace:GetDescendants()) do
    if v.Name == "ComputerTable" then
        table.insert(computadores, v)
    end
end

local idx = 1

-- TP Computador
local tpComp = Instance.new("TextButton", main)
tpComp.Size = UDim2.new(0, 200, 0, 30)
tpComp.Position = UDim2.new(0, 10, 0, 180)
tpComp.Text = "TP Computador"
tpComp.BackgroundColor3 = Color3.fromRGB(60,60,60)
tpComp.TextColor3 = Color3.new(1,1,1)

tpComp.MouseButton1Click:Connect(function()
    local char = player.Character
    if char and computadores[idx] then
        char:MoveTo(computadores[idx].Position + Vector3.new(0,2,0))
    end
    idx += 1
    if idx > #computadores then idx = 1 end
end)

-- ESP Computador
createToggleButton(main, "ESP Computador", UDim2.new(0, 10, 0, 220), function(state)
    for _, comp in pairs(computadores) do
        if state then
            local esp = Instance.new("BillboardGui", comp)
            esp.Name = "ESP"
            esp.Size = UDim2.new(0,100,0,40)
            esp.AlwaysOnTop = true

            local label = Instance.new("TextLabel", esp)
            label.Size = UDim2.new(1,0,1,0)
            label.BackgroundTransparency = 1
            label.Text = "PC"
            label.TextColor3 = Color3.new(0,1,1)
        else
            if comp:FindFirstChild("ESP") then
                comp.ESP:Destroy()
            end
        end
    end
end)

-- ESP Player
createToggleButton(main, "ESP Player", UDim2.new(0, 10, 0, 260), function(state)
    for _, plr in pairs(game.Players:GetPlayers()) do
        if plr ~= player and plr.Character and plr.Character:FindFirstChild("Head") then
            local head = plr.Character.Head
            if state then
                local esp = Instance.new("BillboardGui", head)
                esp.Name = "ESP"
                esp.Size = UDim2.new(0,100,0,40)
                esp.AlwaysOnTop = true

                local label = Instance.new("TextLabel", esp)
                label.Size = UDim2.new(1,0,1,0)
                label.BackgroundTransparency = 1
                label.Text = plr.Name
                label.TextColor3 = Color3.new(0,1,0)
            else
                if head:FindFirstChild("ESP") then
                    head.ESP:Destroy()
                end
            end
        end
    end
end)

-- TP por nome
local box = Instance.new("TextBox", main)
box.Size = UDim2.new(0, 200, 0, 30)
box.Position = UDim2.new(0, 10, 0, 300)
box.PlaceholderText = "Nome do jogador"
box.Text = ""
box.BackgroundColor3 = Color3.fromRGB(60,60,60)
box.TextColor3 = Color3.new(1,1,1)

local tpBtn = Instance.new("TextButton", main)
tpBtn.Size = UDim2.new(0, 200, 0, 30)
tpBtn.Position = UDim2.new(0, 10, 0, 335)
tpBtn.Text = "TP para jogador"
tpBtn.BackgroundColor3 = Color3.fromRGB(60,60,60)
tpBtn.TextColor3 = Color3.new(1,1,1)

tpBtn.MouseButton1Click:Connect(function()
    local name = box.Text:lower()
    for _, plr in pairs(game.Players:GetPlayers()) do
        if plr.Name:lower():sub(1, #name) == name and plr.Character then
            wait(1)
            player.Character:MoveTo(plr.Character:GetPivot().Position + Vector3.new(0,2,0))
            break
        end
    end
end)

-- Botão mini TP rápido
local miniBtnVisible = false

local miniShow = Instance.new("TextButton", main)
miniShow.Size = UDim2.new(0, 200, 0, 30)
miniShow.Position = UDim2.new(0, 10, 0, 370)
miniShow.Text = "Mostrar TP Rápido"
miniShow.BackgroundColor3 = Color3.fromRGB(70,70,70)
miniShow.TextColor3 = Color3.new(1,1,1)

local miniBtn = Instance.new("TextButton", ScreenGui)
miniBtn.Size = UDim2.new(0, 50, 0, 50)
miniBtn.Position = UDim2.new(0.8, 0, 0.5, 0)
miniBtn.Text = "TP"
miniBtn.BackgroundColor3 = Color3.fromRGB(150,150,150)
miniBtn.TextColor3 = Color3.new(0,0,0)
miniBtn.Visible = false
miniBtn.Draggable = true
miniBtn.Active = true

miniShow.MouseButton1Click:Connect(function()
    miniBtnVisible = not miniBtnVisible
    miniBtn.Visible = miniBtnVisible
end)

miniBtn.MouseButton1Click:Connect(function()
    if computadores[idx] and player.Character then
        player.Character:MoveTo(computadores[idx].Position + Vector3.new(0,2,0))
        idx += 1
        if idx > #computadores then idx = 1 end
    end
end)
