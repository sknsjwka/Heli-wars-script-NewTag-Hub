-- Script Completo com Abas: Veículos, Bases, Bandeiras, Armas, Jogadores e Auto Respawn
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
    Name = "HUB Completo",
    LoadingTitle = "Carregando Script...",
    LoadingSubtitle = "Veiculos, Bases, Armas e Jogadores",
    ConfigurationSaving = {Enabled = false},
    KeySystem = false
})

local isTeleporting = false 
local autoReturnDelay = 1 
local autoRespawn = false
local rotinaRodando = false
local ultimaRotina = 0
local jaPegouHeli = false
local posicaoSalva1 = nil
local posicaoSalva2 = nil
local ultimoRespawn = 0
local repetidorAtivo = false
local repetidorDelay = 2
local ultimoRepetidor = 0

local function goToObjectNoReturn(objectName, offsetY)
    local player = game.Players.LocalPlayer
    local char = player.Character or player.CharacterAdded:Wait()
    local hrp = char:WaitForChild("HumanoidRootPart")
    local obj = workspace:FindFirstChild(objectName, true)
    if obj then
        local seat = obj:FindFirstChildWhichIsA("VehicleSeat") or obj:FindFirstChildWhichIsA("Seat")
        local targetPart = obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart") or (obj:IsA("BasePart") and obj)
        if not targetPart and obj:IsA("Folder") then targetPart = obj:FindFirstChildWhichIsA("BasePart", true) end
        local finalCFrame = nil
        if seat then finalCFrame = seat.CFrame + Vector3.new(0, 1, 0)
        elseif targetPart then finalCFrame = targetPart.CFrame * CFrame.new(0, offsetY or 3, 3) end
        if finalCFrame then
            hrp.CFrame = finalCFrame
            hrp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            hrp.AssemblyAngularVelocity = Vector3.new(0, 0, 0)
            Rayfield:Notify({Title = "Sucesso", Content = "Teleportado para: " .. objectName, Duration = 3})
            return true
        end
    end
    Rayfield:Notify({Title = "Erro", Content = objectName .. " não encontrado.", Duration = 3})
    return false
end

local function goToAndReturn(objectName, offsetY, isWeapon)
    if isTeleporting then return end 
    isTeleporting = true
    local player = game.Players.LocalPlayer
    local char = player.Character or player.CharacterAdded:Wait()
    local hrp = char:WaitForChild("HumanoidRootPart")
    local originalPos = hrp.CFrame
    local obj = workspace:FindFirstChild(objectName, true)
    if obj then
        local targetPart = obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart") or (obj:IsA("BasePart") and obj)
        if not targetPart and obj:IsA("Folder") then targetPart = obj:FindFirstChildWhichIsA("BasePart", true) end
        local seat = obj:FindFirstChildWhichIsA("VehicleSeat") or obj:FindFirstChildWhichIsA("Seat")
        local finalCFrame = targetPart and targetPart.CFrame or nil
        if seat then finalCFrame = seat.CFrame + Vector3.new(0, 1, 0)
        elseif targetPart then finalCFrame = targetPart.CFrame * CFrame.new(0, offsetY or 1, 3) end
        if finalCFrame then
            hrp.CFrame = finalCFrame
            hrp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            if isWeapon then
                local st = tick()
                while tick() - st < 10 do
                    task.wait(0.5)
                    local bp = player:FindFirstChild("Backpack")
                    if char:FindFirstChild("MultiRocket") or (bp and bp:FindFirstChild("MultiRocket")) then task.wait(0.2) break end
                end
            else task.wait(autoReturnDelay) end
            if hrp and hrp.Parent then
                hrp.CFrame = originalPos
                hrp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            end
        end
    end
    isTeleporting = false 
end

local function teleportToFlag(teamKeyword)
    local player = game.Players.LocalPlayer
    local char = player.Character or player.CharacterAdded:Wait()
    local hrp = char:WaitForChild("HumanoidRootPart")
    local targetTeam = string.lower(teamKeyword)
    local baseName = string.upper(string.sub(targetTeam, 1, 1)) .. string.sub(targetTeam, 2) .. "Base"
    local flagPart = nil
    local base = workspace:FindFirstChild(baseName, true)
    if base then
        for _, obj in pairs(base:GetDescendants()) do
            if obj:IsA("BasePart") or obj:IsA("Model") then
                local n = string.lower(obj.Name)
                if string.find(n, "flag") or string.find(n, "bandeira") then
                    flagPart = obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart") or obj
                    break
                end
            end
        end
        if not flagPart then
            local mY = -math.huge
            for _, obj in pairs(base:GetDescendants()) do
                if obj:IsA("BasePart") and obj.Position.Y > mY then mY = obj.Position.Y flagPart = obj end
            end
        end
    end
    if not flagPart then
        for _, obj in pairs(workspace:GetDescendants()) do
            if obj:IsA("Model") or obj:IsA("BasePart") then
                local n = string.lower(obj.Name)
                if (string.find(n, "flag") or string.find(n, "bandeira")) and string.find(n, targetTeam) then
                    flagPart = obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart") or obj
                    break
                end
            end
        end
    end
    if flagPart then
        hrp.CFrame = flagPart.CFrame * CFrame.new(0, 3, 5)
        hrp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
        Rayfield:Notify({Title = "Sucesso", Content = "TP bandeira " .. teamKeyword, Duration = 3})
    else
        Rayfield:Notify({Title = "Erro", Content = "Bandeira " .. teamKeyword .. " nao encontrada.", Duration = 3})
    end
end

local function teleportToJungleSpawn()
    local player = game.Players.LocalPlayer
    local char = player.Character or player.CharacterAdded:Wait()
    local hrp = char:WaitForChild("HumanoidRootPart")
    local am = workspace:FindFirstChild("ActiveMap")
    local map = am and am:FindFirstChild("Map")
    local js = map and map:FindFirstChild("JungleSpawns") or workspace:FindFirstChild("JungleSpawns", true)
    if js then
        local spawn = js:FindFirstChildWhichIsA("SpawnLocation", true) or js:FindFirstChildWhichIsA("BasePart", true)
        if spawn then
            hrp.CFrame = spawn.CFrame + Vector3.new(0, 3, 0)
            hrp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            Rayfield:Notify({Title = "Sucesso", Content = "TP Jungle Spawn!", Duration = 3})
            return
        end
    end
    Rayfield:Notify({Title = "Erro", Content = "JungleSpawns nao encontrado.", Duration = 3})
end

local function temMultiRocket()
    local p = game.Players.LocalPlayer
    local c = p.Character
    if not c then return false end
    local bp = p:FindFirstChild("Backpack")
    return (c:FindFirstChild("MultiRocket") ~= nil) or (bp and bp:FindFirstChild("MultiRocket") ~= nil)
end

-- ABA VEICULOS
local VT = Window:CreateTab("Veiculos", 4483362458)
VT:CreateSection("Config")
VT:CreateSlider({Name = "Tempo no veiculo", Range = {0.5, 5}, Increment = 0.5, Suffix = "s", CurrentValue = 1, Callback = function(v) autoReturnDelay = v end})
VT:CreateSection("Jeep")
VT:CreateButton({Name = "Ir ao Jeep (Voltar)", Callback = function() goToAndReturn("Jeep", 5, false) end})
VT:CreateButton({Name = "Ir ao Jeep (So ir)", Callback = function() goToObjectNoReturn("Jeep", 5) end})
VT:CreateSection("SmallHelicopter")
VT:CreateButton({Name = "Ir ao SmallHeli (Voltar)", Callback = function() goToAndReturn("SmallHelicopter", 5, false) end})
VT:CreateButton({Name = "Ir ao SmallHeli (So ir)", Callback = function() goToObjectNoReturn("SmallHelicopter", 5) end})
VT:CreateSection("VipHelicopter")
VT:CreateButton({Name = "Ir ao VipHeli (Voltar)", Callback = function() goToAndReturn("VipHelicopter", 5, false) end})
VT:CreateButton({Name = "Ir ao VipHeli (So ir)", Callback = function() goToObjectNoReturn("VipHelicopter", 5) end})

-- ABA BASES
local BT = Window:CreateTab("Bases", 4483362458)
BT:CreateSection("Teleporte")
BT:CreateButton({Name = "TP para Jungle Spawn", Callback = function() teleportToJungleSpawn() end})
BT:CreateButton({Name = "TP GreenBase", Callback = function() goToObjectNoReturn("GreenBase", 10) end})
BT:CreateButton({Name = "TP TanBase", Callback = function() goToObjectNoReturn("TanBase", 10) end})

BT:CreateSection("Bandeiras")
BT:CreateButton({Name = "TP Bandeira GREEN", Callback = function() teleportToFlag("green") end})
BT:CreateButton({Name = "TP Bandeira TAN", Callback = function() teleportToFlag("tan") end})

BT:CreateButton({Name = "TP Bandeira JUNGLE", Callback = function()
    local p = game.Players.LocalPlayer
    local c = p.Character or p.CharacterAdded:Wait()
    local hrp = c:WaitForChild("HumanoidRootPart")
    local am = workspace:FindFirstChild("ActiveMap")
    local map = am and am:FindFirstChild("Map")
    local fs = map and map:FindFirstChild("FlagStands")
    local flag = nil
    if fs then
        for _, obj in pairs(fs:GetChildren()) do
            if string.find(string.lower(obj.Name), "flag") then
                flag = obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")
                break
            end
        end
    end
    if flag then
        hrp.CFrame = flag.CFrame * CFrame.new(0, 2, -4)
        hrp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
        Rayfield:Notify({Title = "Sucesso", Content = "TP Bandeira Jungle!", Duration = 3})
    else
        Rayfield:Notify({Title = "Erro", Content = "Bandeira Jungle nao encontrada.", Duration = 3})
    end
end})

-- ==========================================
-- SAVE DE POSICAO + REPETIDOR (TUDO JUNTO)
-- ==========================================
BT:CreateSection("Save de Posicao")

BT:CreateButton({
    Name = "SALVAR POSICAO 1",
    Callback = function()
        local p = game.Players.LocalPlayer
        local c = p.Character
        if c and c:FindFirstChild("HumanoidRootPart") then
            posicaoSalva1 = c.HumanoidRootPart.CFrame
            Rayfield:Notify({Title = "Salvo!", Content = "Posicao 1 salva!", Duration = 3})
        end
    end,
})

BT:CreateButton({
    Name = "VOLTAR PARA POSICAO 1",
    Callback = function()
        if not posicaoSalva1 then
            Rayfield:Notify({Title = "Erro", Content = "Nenhuma posicao salva na 1!", Duration = 4})
            return
        end
        local p = game.Players.LocalPlayer
        local c = p.Character
        if c and c:FindFirstChild("HumanoidRootPart") then
            c.HumanoidRootPart.CFrame = posicaoSalva1
            c.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            c.HumanoidRootPart.AssemblyAngularVelocity = Vector3.new(0, 0, 0)
            Rayfield:Notify({Title = "Voltou!", Content = "Voltou para Posicao 1!", Duration = 3})
        end
    end,
})

BT:CreateButton({
    Name = "SALVAR POSICAO 2",
    Callback = function()
        local p = game.Players.LocalPlayer
        local c = p.Character
        if c and c:FindFirstChild("HumanoidRootPart") then
            posicaoSalva2 = c.HumanoidRootPart.CFrame
            Rayfield:Notify({Title = "Salvo!", Content = "Posicao 2 salva!", Duration = 3})
        end
    end,
})

BT:CreateButton({
    Name = "VOLTAR PARA POSICAO 2",
    Callback = function()
        if not posicaoSalva2 then
            Rayfield:Notify({Title = "Erro", Content = "Nenhuma posicao salva na 2!", Duration = 4})
            return
        end
        local p = game.Players.LocalPlayer
        local c = p.Character
        if c and c:FindFirstChild("HumanoidRootPart") then
            c.HumanoidRootPart.CFrame = posicaoSalva2
            c.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            c.HumanoidRootPart.AssemblyAngularVelocity = Vector3.new(0, 0, 0)
            Rayfield:Notify({Title = "Voltou!", Content = "Voltou para Posicao 2!", Duration = 3})
        end
    end,
})

-- Repetidor na mesma seção
BT:CreateSlider({
    Name = "Tempo do Repetidor",
    Range = {0.5, 10},
    Increment = 0.5,
    Suffix = "s",
    CurrentValue = 2,
    Callback = function(v) repetidorDelay = v end,
})

BT:CreateToggle({
    Name = "Ativar Repetidor (P1 <-> P2)",
    CurrentValue = false,
    Callback = function(v)
        repetidorAtivo = v
        if v then
            if not posicaoSalva1 or not posicaoSalva2 then
                Rayfield:Notify({Title = "Aviso", Content = "Salve as 2 posicoes antes!", Duration = 4})
                repetidorAtivo = false
                return
            end
            Rayfield:Notify({Title = "Repetidor", Content = "Ligado! Alternando P1 e P2.", Duration = 3})
        else
            Rayfield:Notify({Title = "Repetidor", Content = "Desligado.", Duration = 2})
        end
    end,
})

-- Loop do Repetidor
task.spawn(function()
    while task.wait(0.1) do
        if repetidorAtivo and posicaoSalva1 and posicaoSalva2 then
            if tick() - ultimoRepetidor >= repetidorDelay then
                ultimoRepetidor = tick()
                local p = game.Players.LocalPlayer
                local c = p.Character
                if c and c:FindFirstChild("HumanoidRootPart") and c:FindFirstChildOfClass("Humanoid").Health > 0 then
                    local hrp = c.HumanoidRootPart
                    local d1 = (hrp.Position - posicaoSalva1.Position).Magnitude
                    local d2 = (hrp.Position - posicaoSalva2.Position).Magnitude
                    if d1 < d2 then
                        hrp.CFrame = posicaoSalva2
                    else
                        hrp.CFrame = posicaoSalva1
                    end
                    hrp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
                    hrp.AssemblyAngularVelocity = Vector3.new(0, 0, 0)
                end
            end
        end
    end
end)

-- ABA ARMAS
local AT = Window:CreateTab("Armas", 4483362458)
AT:CreateSection("MultiRocket")
AT:CreateButton({Name = "Pegar MultiRocket e Voltar", Callback = function() goToAndReturn("Multi-Rocket Giver", 1, true) end})

-- ABA AUTO RESPAWN
local RT = Window:CreateTab("Auto Respawn", 4483362458)
RT:CreateSection("Auto Respawn")
RT:CreateToggle({Name = "Ativar Auto Respawn", CurrentValue = false, Callback = function(v)
    autoRespawn = v
    if v then jaPegouHeli = false end
end})

local function rotinaRespawn()
    if rotinaRodando then return end
    if tick() - ultimaRotina < 5 then return end
    rotinaRodando = true
    ultimaRotina = tick()
    local p = game.Players.LocalPlayer
    local c = p.Character
    if not c then rotinaRodando = false return end
    local h = c:WaitForChild("Humanoid")
    local hrp = c:WaitForChild("HumanoidRootPart")
    task.wait(0.5)
    if h.Health <= 0 then rotinaRodando = false return end
    local sp = hrp.CFrame
    if not temMultiRocket() then
        local g = workspace:FindFirstChild("Multi-Rocket Giver", true)
        if g then
            local tp = g.PrimaryPart or g:FindFirstChildWhichIsA("BasePart")
            if tp then
                hrp.CFrame = tp.CFrame * CFrame.new(0, 2, 3)
                hrp.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            end
        end
        local st = tick()
        while tick() - st < 5 do
            task.wait(0.3)
            if temMultiRocket() then break end
        end
        task.wait(0.3)
    end
    local c2 = p.Character
    if not c2 or not c2:FindFirstChild("HumanoidRootPart") or c2:FindFirstChildOfClass("Humanoid").Health <= 0 then rotinaRodando = false return end
    c2.HumanoidRootPart.CFrame = sp
    c2.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
    task.wait(0.5)
    local he = workspace:FindFirstChild("VipHelicopter", true)
    if he then
        local s = he:FindFirstChildWhichIsA("VehicleSeat") or he:FindFirstChildWhichIsA("Seat")
        local c3 = p.Character
        if c3 and c3:FindFirstChild("HumanoidRootPart") and c3:FindFirstChildOfClass("Humanoid").Health > 0 then
            if s then c3.HumanoidRootPart.CFrame = s.CFrame + Vector3.new(0, 1, 0)
            else c3.HumanoidRootPart.CFrame = he.PrimaryPart.CFrame + Vector3.new(0, 2, 0) end
            c3.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
            jaPegouHeli = true
        end
    end
    rotinaRodando = false
end

game.Players.LocalPlayer.CharacterAdded:Connect(function()
    local agora = tick()
    if agora - ultimoRespawn < 3 then return end
    ultimoRespawn = agora
    jaPegouHeli = false
    if autoRespawn then 
        task.wait(0.5) 
        task.spawn(rotinaRespawn) 
    end
end)

-- ABA JOGADORES
local JT = Window:CreateTab("Jogadores", 4483362458)
local function getP()
    local l = {}
    for _, p in pairs(game.Players:GetPlayers()) do
        if p ~= game.Players.LocalPlayer then
            local tn = p.Team and p.Team.Name or "Sem Time"
            table.insert(l, p.Name .. " [" .. tn .. "]")
        end
    end
    return l
end
local sel = ""
local dd
JT:CreateSection("Jogadores")
dd = JT:CreateDropdown({Name = "Lista", Options = {""}, CurrentOption = {""}, Callback = function(o) sel = string.match(o[1], "^(.-) %[") or o[1] end})
JT:CreateButton({Name = "Confirmar TP", Callback = function()
    if sel == "" then return end
    local t = game.Players:FindFirstChild(sel)
    if t and t.Character and t.Character:FindFirstChild("HumanoidRootPart") then
        local c = game.Players.LocalPlayer.Character
        if c and c:FindFirstChild("HumanoidRootPart") then
            c.HumanoidRootPart.CFrame = t.Character.HumanoidRootPart.CFrame * CFrame.new(0, 5, -5)
            c.HumanoidRootPart.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
        end
    end
end})
JT:CreateButton({Name = "Atualizar Lista", Callback = function() if dd and dd.Refresh then dd:Refresh(getP()) end end})
task.spawn(function() task.wait(3) if dd and dd.Refresh then dd:Refresh(getP()) end end)
game.Players.PlayerAdded:Connect(function() task.wait(1) if dd and dd.Refresh then dd:Refresh(getP()) end end)
game.Players.PlayerRemoving:Connect(function() task.wait(1) if dd and dd.Refresh then dd:Refresh(getP()) end end)
