# #469 - test [Closed]

> Username: dolganovyura1993-creator  
> Created at: 2025-11-20 06:43:36 UTC  
> Updated at: 2025-11-20 08:00:07 UTC  
> Closed at: 2025-11-20 08:00:07 UTC  
> Url: https://github.com/boostorg/test/issues/469  

-- Улучшенная система кэширования  
local cache = {  
    players = {},  
    drones = {},  
    teamCache = {},  
    lastTeamUpdate = 0,  
    TEAM_UPDATE_INTERVAL = 5  
}  
  
-- Улучшенная система поиска целей с кэшированием  
local function findTargets()  
    local targets = {}  
    local currentTime = tick()  
      
    -- Обновляем команды только если прошло достаточно времени  
    if currentTime - cache.lastTeamUpdate > cache.TEAM_UPDATE_INTERVAL then  
        updateTeams()  
        cache.lastTeamUpdate = currentTime  
    end  
      
    -- Кэширование игроков  
    for _, player in pairs(Players:GetPlayers()) do  
        if player ~= localPlayer and player.Character then  
            local character = player.Character  
            local humanoid = character:FindFirstChildOfClass("Humanoid")  
              
            if humanoid and humanoid.Health > 0 then  
                local isEnemy = isEnemyPlayer(player)  
                if isEnemy then  
                    targets[character] = {  
                        Object = character,  
                        Type = "Enemy",  
                        Name = player.Name,  
                        Player = player,  
                        Humanoid = humanoid,  
                        LastUpdate = currentTime  
                    }  
                end  
            end  
        end  
    end  
      
    -- Улучшенный поиск дронов с кэшированием  
    if Settings.ESP.ShowDrones then  
        for _, obj in pairs(workspace:GetChildren()) do  
            if obj:IsA("Model") and not targets[obj] then  
                local nameLower = obj.Name:lower()  
                local isDrone = nameLower:find("drone") or   
                              nameLower:find("fpv") or   
                              nameLower:find("quadcopter") or   
                              nameLower:find("uav")  
                  
                if isDrone then  
                    local rootPart = obj.PrimaryPart or   
                                   obj:FindFirstChild("HumanoidRootPart") or   
                                   obj:FindFirstChild("Head")  
                      
                    if not rootPart then  
                        -- Быстрый поиск любой BasePart  
                        for _, part in pairs(obj:GetChildren()) do  
                            if part:IsA("BasePart") then  
                                rootPart = part  
                                break  
                            end  
                        end  
                    end  
                      
                    if rootPart then  
                        local velocity = rootPart.AssemblyLinearVelocity.Magnitude  
                        local isFlying = rootPart.Position.Y > 5 or velocity > 1  
                          
                        if isFlying then  
                            targets[obj] = {  
                                Object = obj,  
                                Type = "Drone",  
                                Name = "FPV Дрон",  
                                RootPart = rootPart,  
                                LastUpdate = currentTime  
                            }  
                        end  
                    end  
                end  
            end  
        end  
    end  
      
    return targets  
end  
  
-- Улучшенная система обновления ESP с LOD (Level of Detail)  
local function updateESPWithLOD(targetData)  
    local target = targetData.Target.Object  
    local espFrame = targetData.Frame  
      
    if not target or not target.Parent then  
        espFrame.Visible = false  
        if targetData.Outline then  
            targetData.Outline.Enabled = false  
        end  
        if targetData.BoxESP then  
            targetData.BoxESP.Visible = false  
        end  
        removeChams(target)  
        return false  
    end  
      
    local rootPart = target:FindFirstChild("HumanoidRootPart") or   
                    target:FindFirstChild("Head") or   
                    target.PrimaryPart or   
                    targetData.Target.RootPart  
      
    if not rootPart then  
        espFrame.Visible = false  
        if targetData.Outline then  
            targetData.Outline.Enabled = false  
        end  
        if targetData.BoxESP then  
            targetData.BoxESP.Visible = false  
        end  
        removeChams(target)  
        return false  
    end  
      
    local success, screenPos, onScreen = pcall(function()  
        return camera:WorldToViewportPoint(rootPart.Position)  
    end)  
      
    if not success or not onScreen then  
        espFrame.Visible = false  
        if targetData.Outline then  
            targetData.Outline.Enabled = false  
        end  
        if targetData.BoxESP then  
            targetData.BoxESP.Visible = false  
        end  
        removeChams(target)  
        return false  
    end  
      
    local distance = (camera.CFrame.Position - rootPart.Position).Magnitude  
    local maxDistance = targetData.Target.Type == "Drone" and Settings.ESP.DroneDistance or Settings.ESP.MaxDistance  
      
    if distance > maxDistance then  
        espFrame.Visible = false  
        if targetData.Outline then  
            targetData.Outline.Enabled = false  
        end  
        if targetData.BoxESP then  
            targetData.BoxESP.Visible = false  
        end  
        return false  
    end  
      
    -- LOD система: уменьшаем детализацию для дальних объектов  
    local lodLevel = 1  
    if distance > 300 then lodLevel = 2 end  
    if distance > 600 then lodLevel = 3 end  
      
    -- Применяем настройки видимости  
    local shouldShow = Settings.ESP.Enabled  
      
    if targetData.Target.Type == "Enemy" then  
        shouldShow = shouldShow and Settings.ESP.ShowEnemies  
        if targetData.Target.Player then  
            shouldShow = shouldShow and isEnemyPlayer(targetData.Target.Player)  
        end  
    elseif targetData.Target.Type == "Drone" then  
        shouldShow = shouldShow and Settings.ESP.ShowDrones  
    end  
      
    if not shouldShow then  
        espFrame.Visible = false  
        if targetData.Outline then  
            targetData.Outline.Enabled = false  
        end  
        if targetData.BoxESP then  
            targetData.BoxESP.Visible = false  
        end  
        removeChams(target)  
        return false  
    end  
      
    -- Применяем визуальные эффекты  
    if Settings.Visuals.Chams then  
        applyChams(target)  
    else  
        removeChams(target)  
    end  
      
    -- Обновляем контуры  
    if targetData.Outline then  
        targetData.Outline.Enabled = Settings.Visuals.OutlineModels and lodLevel < 3  
        targetData.Outline.OutlineColor = targetData.Target.Type == "Drone" and COLORS.Warning or Settings.Visuals.ChamsColor  
    end  
      
    -- Обновляем Box ESP (только для ближних дистанций)  
    if targetData.BoxESP and targetData.Target.Type == "Enemy" and lodLevel == 1 then  
        updateBoxESP(targetData.BoxESP, target, screenPos, 100)  
        targetData.BoxESP.Visible = Settings.Visuals.OutlineModels  
    elseif targetData.BoxESP then  
        targetData.BoxESP.Visible = false  
    end  
      
    -- Обновляем информационную панель  
    local infoPanel = espFrame:FindFirstChild("InfoPanel")  
    if infoPanel then  
        -- Позиционирование с учетом LOD  
        local panelSize = lodLevel == 1 and UDim2.new(0, 120, 0, 45) or  
                         lodLevel == 2 and UDim2.new(0, 100, 0, 35) or  
                         UDim2.new(0, 80, 0, 25)  
          
        infoPanel.Size = panelSize  
          
        local targetPosition = UDim2.new(0, screenPos.X - panelSize.X.Offset/2, 0, screenPos.Y - panelSize.Y.Offset - 20)  
          
        -- Сглаживание позиции  
        if targetData.SmoothPosition == UDim2.new(0, 0, 0, 0) then  
            targetData.SmoothPosition = targetPosition  
            infoPanel.Position = targetPosition  
        else  
            local alpha = 0.3  
            local posDelta = math.abs(targetPosition.X.Offset - targetData.SmoothPosition.X.Offset) +   
                           math.abs(targetPosition.Y.Offset - targetData.SmoothPosition.Y.Offset)  
              
            if posDelta > 100 then alpha = 0.6  
            elseif posDelta > 50 then alpha = 0.4 end  
              
            smoothUpdatePosition(infoPanel, targetPosition, alpha)  
            targetData.SmoothPosition = infoPanel.Position  
        end  
          
        infoPanel.Visible = Settings.ESP.ShowNames and lodLevel < 3  
          
        -- Обновляем элементы панели  
        local nameLabel = infoPanel:FindFirstChild("NameLabel")  
        local distanceLabel = infoPanel:FindFirstChild("DistanceLabel")  
        local healthBar = infoPanel:FindFirstChild("HealthBar")  
          
        if nameLabel then  
            nameLabel.Text = targetData.Target.Name  
            nameLabel.TextColor3 = targetData.Target.Type == "Drone" and COLORS.Warning or Settings.Visuals.ChamsColor  
            nameLabel.TextSize = lodLevel == 1 and 11 or lodLevel == 2 and 10 or 9  
        end  
          
        if distanceLabel then  
            distanceLabel.Text = math.floor(distance) .. "m"  
            distanceLabel.Visible = Settings.Visuals.ShowDistance and lodLevel < 3  
            distanceLabel.TextColor3 = targetData.Target.Type == "Drone" and COLORS.Warning or COLORS.Secondary  
            distanceLabel.TextSize = lodLevel == 1 and 10 or 9  
        end  
          
        if healthBar and targetData.Target.Type == "Enemy" then  
            local humanoid = target:FindFirstChildOfClass("Humanoid")  
            if humanoid and humanoid.MaxHealth > 0 then  
                local newHealthPercent = math.clamp(humanoid.Health / humanoid.MaxHealth, 0, 1)  
                  
                local healthAlpha = 0.5  
                targetData.HealthPercent = targetData.HealthPercent + (newHealthPercent - targetData.HealthPercent) * healthAlpha  
                  
                local healthFill = healthBar:FindFirstChild("HealthFill")  
                if healthFill then  
                    smoothHealthUpdate(healthFill, UDim2.new(targetData.HealthPercent, 0, 1, 0), 0.8)  
                      
                    -- Градиент здоровья от зеленого к красному  
                    if targetData.HealthPercent > 0.5 then  
                        healthFill.BackgroundColor3 = Color3.fromRGB(  
                            (1 - targetData.HealthPercent) * 255 * 2,  
                            255,  
                            0  
                        )  
                    else  
                        healthFill.BackgroundColor3 = Color3.fromRGB(  
                            255,  
                            targetData.HealthPercent * 255 * 2,  
                            0  
                        )  
                    end  
                end  
                healthBar.Visible = Settings.Visuals.ShowHealth and lodLevel < 3  
            else  
                healthBar.Visible = false  
            end  
        elseif healthBar then  
            healthBar.Visible = false  
        end  
    end  
      
    espFrame.Visible = true  
    targetData.LastUpdate = tick()  
    targetData.LastScreenPos = Vector2.new(screenPos.X, screenPos.Y)  
    return true  
end  
  
-- Улучшенная система очистки кэша  
local function cleanupCache()  
    local currentTime = tick()  
    local cleanupInterval = 10 -- секунд  
      
    for target, data in pairs(espCache) do  
        if currentTime - data.LastUpdate > cleanupInterval then  
            -- Удаляем старые объекты из кэша  
            data.Frame:Destroy()  
            if data.Outline then  
                data.Outline:Destroy()  
            end  
            if data.BoxESP then  
                data.BoxESP:Destroy()  
            end  
            removeChams(target)  
            espCache[target] = nil  
        end  
    end  
      
    -- Очистка кэша XRay  
    for part, _ in pairs(xrayCache) do  
        if not part or not part.Parent then  
            xrayCache[part] = nil  
        end  
    end  
      
    -- Очистка кэша Chams  
    for target, highlight in pairs(chamsCache) do  
        if not target or not target.Parent then  
            highlight:Destroy()  
            chamsCache[target] = nil  
        end  
    end  
end  
  
-- Улучшенная система обновления ESP с батчингом  
local function updateESPSystem()  
    local currentTime = tick()  
      
    -- Ограничение частоты обновления  
    if currentTime - lastUpdate < UPDATE_INTERVAL then  
        return  
    end  
    lastUpdate = currentTime  
      
    -- Периодическая очистка кэша  
    if currentTime % 5 < UPDATE_INTERVAL then  
        cleanupCache()  
    end  
      
    if not Settings.ESP.Enabled then  
        for target, data in pairs(espCache) do  
            data.Frame.Visible = false  
            if data.Outline then  
                data.Outline.Enabled = false  
            end  
            if data.BoxESP then  
                data.BoxESP.Visible = false  
            end  
            removeChams(target)  
        end  
        return  
    end  
      
    -- Получаем цели с кэшированием  
    local targets = findTargets()  
    local currentTargets = {}  
      
    -- Создаем ESP для новых целей  
    for target, targetData in pairs(targets) do  
        if not espCache[target] then  
            createESP(targetData)  
        end  
        currentTargets[target] = true  
    end  
      
    -- Удаляем старые цели  
    for target, data in pairs(espCache) do  
        if not currentTargets[target] then  
            data.Frame:Destroy()  
            if data.Outline then  
                data.Outline:Destroy()  
            end  
            if data.BoxESP then  
                data.BoxESP:Destroy()  
            end  
            removeChams(target)  
            espCache[target] = nil  
        end  
    end  
      
    -- Обновляем ESP для всех активных целей  
    for target, data in pairs(espCache) do  
        updateESPWithLOD(data)  
    end  
end  
  
-- Улучшенная система XRay с оптимизацией  
local function applyXRay()  
    if not Settings.Visuals.XRay then  
        -- Восстанавливаем прозрачность  
        for part, originalTransparency in pairs(xrayCache) do  
            if part and part.Parent then  
                pcall(function()  
                    part.Transparency = originalTransparency  
                end)  
            end  
        end  
        xrayCache = {}  
        return  
    end  
      
    local currentTime = tick()  
      
    -- Ограничиваем количество обрабатываемых частей для производительности  
    local processed = 0  
    local maxProcessed = 1000  
      
    for _, obj in pairs(workspace:GetDescendants()) do  
        if processed >= maxProcessed then break end  
          
        if obj:IsA("BasePart") and obj.Transparency < Settings.Visuals.XRayTransparency then  
            if not xrayCache[obj] then  
                xrayCache[obj] = obj.Transparency  
            end  
            pcall(function()  
                obj.Transparency = Settings.Visuals.XRayTransparency  
            end)  
            processed = processed + 1  
        end  
    end  
end  
  
-- Улучшенная система определения команд  
local function updateTeams()  
    if not findTeamsModel() then  
        -- Если модель команд не найдена, используем резервную систему  
        cache.teamCache[localPlayer] = "default"  
        return  
    end  
      
    localPlayerTeam = getPlayerTeam(localPlayer)  
      
    enemyTeams = {}  
    cache.teamCache = {}  
      
    -- Кэшируем команды всех игроков  
    for _, player in pairs(Players:GetPlayers()) do  
        local team = getPlayerTeam(player)  
        cache.teamCache[player] = team  
          
        if team and team ~= localPlayerTeam then  
            table.insert(enemyTeams, team)  
        end  
    end  
      
    -- Убираем дубликаты команд  
    local uniqueTeams = {}  
    for _, team in ipairs(enemyTeams) do  
        uniqueTeams[team] = true  
    end  
    enemyTeams = {}  
    for team, _ in pairs(uniqueTeams) do  
        table.insert(enemyTeams, team)  
    end  
end  
  
-- Улучшенная функция определения врага с кэшированием  
local function isEnemyPlayer(player)  
    if not teamsModel then  
        return player ~= localPlayer  
    end  
      
    if not localPlayerTeam then  
        return player ~= localPlayer  
    end  
      
    -- Используем кэш для быстрого определения  
    local playerTeam = cache.teamCache[player]  
    if not playerTeam then  
        playerTeam = getPlayerTeam(player)  
        cache.teamCache[player] = playerTeam  
    end  
      
    return playerTeam ~= localPlayerTeam  
end  
  
-- Улучшенная система создания ESP с пулом объектов  
local espPool = {  
    frames = {},  
    outlines = {},  
    boxes = {}  
}  
  
local function getFromPool(pool, objectType)  
    for i, obj in ipairs(pool) do  
        if not obj.Visible then  
            table.remove(pool, i)  
            return obj  
        end  
    end  
    return nil  
end  
  
local function createESP(target)  
    if espCache[target.Object] then return end  
      
    -- Пытаемся использовать объекты из пула  
    local espFrame = getFromPool(espPool.frames, "Frame") or Instance.new("Frame")  
    espFrame.Name = "ESP_" .. target.Object.Name  
    espFrame.BackgroundTransparency = 1  
    espFrame.Size = UDim2.new(1, 0, 1, 0)  
    espFrame.ZIndex = 10  
    espFrame.Visible = false  
    espFrame.Parent = screenGui  
      
    local outline = nil  
    if Settings.Visuals.OutlineModels then  
        outline = getFromPool(espPool.outlines, "Highlight") or createModelOutline(target)  
        outline.Adornee = target.Object  
        outline.Parent = screenGui  
    end  
      
    local boxESP = createBoxESP(target)  
      
    if Settings.Visuals.Chams then  
        applyChams(target.Object)  
    end  
      
    -- Создаем информационную панель (оптимизированную версию)  
    local infoPanel = espFrame:FindFirstChild("InfoPanel")  
    if not infoPanel then  
        infoPanel = Instance.new("Frame")  
        infoPanel.Name = "InfoPanel"  
        infoPanel.BackgroundTransparency = 0.85  
        infoPanel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)  
        infoPanel.Size = UDim2.new(0, 120, 0, 45)  
        infoPanel.ZIndex = 12  
        infoPanel.Visible = Settings.ESP.ShowNames  
        infoPanel.Parent = espFrame  
          
        local UICorner = Instance.new("UICorner")  
        UICorner.CornerRadius = UDim.new(0, 6)  
        UICorner.Parent = infoPanel  
          
        -- Создаем остальные элементы панели...  
        -- [остальной код создания элементов остается таким же]  
    end  
      
    espCache[target.Object] = {  
        Frame = espFrame,  
        Target = target,  
        Outline = outline,  
        BoxESP = boxESP,  
        LastUpdate = 0,  
        SmoothPosition = UDim2.new(0, 0, 0, 0),  
        LastScreenPos = Vector2.new(0, 0),  
        HealthPercent = 1.0  
    }  
end  
  
-- Улучшенная система обработки ошибок  
local function safeCall(func, errorMessage)  
    local success, result = pcall(func)  
    if not success then  
        print("⚠️ Ошибка: " .. errorMessage)  
        return nil  
    end  
    return result  
end  
  
-- Добавляем функцию для оптимизации рендеринга  
local function setRenderingOptimizations()  
    -- Отключаем ненужные визуальные эффекты для повышения производительности  
    if settings() then  
        pcall(function()  
            settings().Rendering.QualityLevel = 1  
        end)  
    end  
end  
  
-- Инициализация улучшений  
local function initOptimizations()  
    setRenderingOptimizations()  
      
    -- Предварительная загрузка пула объектов  
    for i = 1, 20 do  
        local frame = Instance.new("Frame")  
        frame.Visible = false  
        table.insert(espPool.frames, frame)  
    end  
      
    print("🔄 Улучшенная система ESP инициализирована")  
    print("🎯 Включены оптимизации: LOD, кэширование, пул объектов")  
    print("📊 Максимальная производительность активирована")  
end  
  
-- Заменяем основную функцию main на улучшенную версию  
local originalMain = main  
main = function()  
    camera = workspace.CurrentCamera  
    if camera then  
        originalFOV = camera.FieldOfView  
    end  
      
    -- Инициализируем оптимизации  
    initOptimizations()  
      
    findTeamsModel()  
    updateTeams()  
      
    createGUI()  
    createCheatMenu()  
    setupRespawn()  
    setupHotkeys()  
      
    -- Заменяем соединение на улучшенную версию  
    if connections.render then  
        connections.render:Disconnect()  
    end  
      
    connections.render = RunService.RenderStepped:Connect(function()  
        safeCall(function()  
            updateESPSystem()  
            updateFOV()  
        end, "Ошибка в основном цикле рендеринга")  
    end)  
      
    -- Фоновая синхронизация команд  
    spawn(function()  
        while true do  
            wait(15) -- Увеличили интервал для снижения нагрузки  
            safeCall(updateTeams, "Ошибка обновления команд")  
        end  
    end)  
      
    print("🟢 УЛУЧШЕННАЯ ESP загружена!")  
    print("⚡ Производительность оптимизирована")  
    print("🎮 Горячие клавиши остались прежними")  
    print("📊 LOD система: детализация зависит от дистанции")  
    print("💾 Кэширование: уменьшена нагрузка на процессор")  
end  
  
-- Запускаем улучшенную версию  
main()
