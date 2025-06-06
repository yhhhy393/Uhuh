-- // Configurações
local TeamCheck = true
local UpdateRate = 0.1 -- tempo entre atualizações
local ESPColor = Color3.fromRGB(255, 0, 0)
local TeamColor = Color3.fromRGB(0, 255, 0)

-- // Tabela de ESPs criados
local ESPTable = {}

-- // Função para criar a box ESP
function CreateESP(player)
    local box = Drawing.new("Text")
    box.Visible = false
    box.Center = true
    box.Outline = true
    box.Font = 2
    box.Size = 13
    box.Color = ESPColor
    ESPTable[player] = box
end

-- // Remover ESP ao sair
game.Players.PlayerRemoving:Connect(function(player)
    if ESPTable[player] then
        ESPTable[player]:Remove()
        ESPTable[player] = nil
    end
end)

-- // Criar ESPs iniciais
for _, player in pairs(game.Players:GetPlayers()) do
    if player ~= game.Players.LocalPlayer then
        CreateESP(player)
    end
end

-- // Atualizar ESPs para novos players
game.Players.PlayerAdded:Connect(function(player)
    if player ~= game.Players.LocalPlayer then
        CreateESP(player)
    end
end)

-- // Loop de atualização
spawn(function()
    while task.wait(UpdateRate) do
        for player, box in pairs(ESPTable) do
            if player.Character and player.Character:FindFirstChild("HumanoidRootPart") and player.Character:FindFirstChild("Humanoid") and player.Character.Humanoid.Health > 0 then
                local hrp = player.Character.HumanoidRootPart
                local pos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(hrp.Position)
                
                if onScreen then
                    -- Checar time se ativado
                    if TeamCheck and player.Team == game.Players.LocalPlayer.Team then
                        box.Visible = false
                    else
                        box.Position = Vector2.new(pos.X, pos.Y)
                        box.Text = player.Name
                        box.Color = TeamCheck and ESPColor or TeamColor
                        box.Visible = true
                    end
                else
                    box.Visible = false
                end
            else
                box.Visible = false
            end
        end
    end
end)

print("[ESP] Ativado com verificação de time:", TeamCheck)
