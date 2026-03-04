--[[
    SISTEMA FPS EM PRIMEIRA PESSOA - ROBLOX
    EdgarHub - Configurações principais no topo para fácil ajuste
]]

-- ========== CONFIGURAÇÕES ==========
local CONFIG = {
    -- Distâncias
    MAX_DISTANCE = 1000,           -- Distância máxima para detectar jogadores
    FOV_RADIUS = 300,              -- Raio inicial do FOV em pixels
    
    -- Cores
    MENU_COLOR = Color3.fromRGB(30, 30, 35),
    BUTTON_COLOR = Color3.fromRGB(45, 45, 50),
    BUTTON_HOVER = Color3.fromRGB(60, 60, 65),
    TEXT_COLOR = Color3.fromRGB(255, 255, 255),
    FOV_COLOR = Color3.fromRGB(255, 255, 255),
    EDGAR_RED = Color3.fromRGB(255, 50, 50),      -- Vermelho característico do EdgarHub
    
    -- ESP Cores
    ESP_BOX_COLOR = Color3.fromRGB(255, 0, 0),
    ESP_SKELETON_COLOR = Color3.fromRGB(0, 255, 0),
    ESP_LINE_COLOR = Color3.fromRGB(0, 0, 255),
}

-- ========== VARIÁVEIS GLOBAIS ==========
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

-- Estados das funcionalidades
local settings = {
    AimAssist = false,
    ESP = false,
    ESPType = "Box",               -- Tipos: "Box", "Skeleton", "Line"
    TeamCheck = false,
    FOVSize = CONFIG.FOV_RADIUS,
}

-- Armazenar objetos da GUI
local guiObjects = {}

-- ========== CRIAÇÃO DA INTERFACE ==========
local function createInterface()
    -- Criar ScreenGui principal
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "EdgarHub_FPSMenu"
    screenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
    
    -- ===== MENU PRINCIPAL =====
    local menuFrame = Instance.new("Frame")
    menuFrame.Name = "MenuFrame"
    menuFrame.Size = UDim2.new(0, 250, 0, 350)
    menuFrame.Position = UDim2.new(0, 20, 0.5, -175)
    menuFrame.BackgroundColor3 = CONFIG.MENU_COLOR
    menuFrame.BackgroundTransparency = 0.1
    menuFrame.BorderSizePixel = 0
    menuFrame.Visible = false
    menuFrame.Parent = screenGui
    
    -- Arredondar cantos do menu
    local menuCorner = Instance.new("UICorner")
    menuCorner.CornerRadius = UDim.new(0, 10)
    menuCorner.Parent = menuFrame
    
    -- Título do menu com logo EdgarHub
    local titleFrame = Instance.new("Frame")
    titleFrame.Size = UDim2.new(1, 0, 0, 40)
    titleFrame.Position = UDim2.new(0, 0, 0, 0)
    titleFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
    titleFrame.BackgroundTransparency = 0.3
    titleFrame.BorderSizePixel = 0
    titleFrame.Parent = menuFrame
    
    local titleCorner = Instance.new("UICorner")
    titleCorner.CornerRadius = UDim.new(0, 10)
    titleCorner.Parent = titleFrame
    
    -- Letra E vermelha do EdgarHub
    local eLabel = Instance.new("TextLabel")
    eLabel.Size = UDim2.new(0, 30, 1, 0)
    eLabel.Position = UDim2.new(0, 10, 0, 0)
    eLabel.BackgroundTransparency = 1
    eLabel.Text = "E"
    eLabel.TextColor3 = CONFIG.EDGAR_RED
    eLabel.TextScaled = true
    eLabel.Font = Enum.Font.GothamBold
    eLabel.TextXAlignment = Enum.TextXAlignment.Center
    eLabel.Parent = titleFrame
    
    -- Texto "dgarHub" ao lado do E vermelho
    local hubLabel = Instance.new("TextLabel")
    hubLabel.Size = UDim2.new(0, 100, 1, 0)
    hubLabel.Position = UDim2.new(0, 40, 0, 0)
    hubLabel.BackgroundTransparency = 1
    hubLabel.Text = "dgarHub"
    hubLabel.TextColor3 = CONFIG.TEXT_COLOR
    hubLabel.TextScaled = true
    hubLabel.Font = Enum.Font.GothamBold
    hubLabel.TextXAlignment = Enum.TextXAlignment.Left
    hubLabel.Parent = titleFrame
    
    -- Versão
    local versionLabel = Instance.new("TextLabel")
    versionLabel.Size = UDim2.new(0, 50, 1, 0)
    versionLabel.Position = UDim2.new(1, -60, 0, 0)
    versionLabel.BackgroundTransparency = 1
    versionLabel.Text = "v1.0"
    versionLabel.TextColor3 = Color3.fromRGB(150, 150, 150)
    versionLabel.TextScaled = true
    versionLabel.Font = Enum.Font.Gotham
    versionLabel.TextXAlignment = Enum.TextXAlignment.Right
    versionLabel.Parent = titleFrame
    
    -- ===== BOTÕES DO MENU =====
    local function createToggleButton(parent, name, yPos, defaultValue, callback)
        local buttonFrame = Instance.new("Frame")
        buttonFrame.Size = UDim2.new(0.9, 0, 0, 40)
        buttonFrame.Position = UDim2.new(0.05, 0, 0, yPos)
        buttonFrame.BackgroundColor3 = CONFIG.BUTTON_COLOR
        buttonFrame.Parent = parent
        
        local buttonCorner = Instance.new("UICorner")
        buttonCorner.CornerRadius = UDim.new(0, 5)
        buttonCorner.Parent = buttonFrame
        
        local buttonText = Instance.new("TextLabel")
        buttonText.Size = UDim2.new(0.7, 0, 1, 0)
        buttonText.Position = UDim2.new(0, 10, 0, 0)
        buttonText.BackgroundTransparency = 1
        buttonText.Text = name
        buttonText.TextColor3 = CONFIG.TEXT_COLOR
        buttonText.TextXAlignment = Enum.TextXAlignment.Left
        buttonText.Font = Enum.Font.Gotham
        buttonText.TextScaled = true
        buttonText.Parent = buttonFrame
        
        local toggleButton = Instance.new("TextButton")
        toggleButton.Size = UDim2.new(0.2, 0, 0.7, 0)
        toggleButton.Position = UDim2.new(0.75, 0, 0.15, 0)
        toggleButton.BackgroundColor3 = defaultValue and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)
        toggleButton.Text = defaultValue and "ON" or "OFF"
        toggleButton.TextColor3 = CONFIG.TEXT_COLOR
        toggleButton.Font = Enum.Font.GothamBold
        toggleButton.TextScaled = true
        toggleButton.Parent = buttonFrame
        
        local toggleCorner = Instance.new("UICorner")
        toggleCorner.CornerRadius = UDim.new(0, 5)
        toggleCorner.Parent = toggleButton
        
        -- Função do botão toggle
        toggleButton.MouseButton1Click:Connect(function()
            local newState = not (toggleButton.Text == "ON")
            toggleButton.BackgroundColor3 = newState and Color3.fromRGB(0, 255, 0) or Color3.fromRGB(255, 0, 0)
            toggleButton.Text = newState and "ON" or "OFF"
            callback(newState)
        end)
        
        return buttonFrame
    end
    
    -- Botões principais
    createToggleButton(menuFrame, "Aim Assist", 50, settings.AimAssist, function(state)
        settings.AimAssist = state
    end)
    
    createToggleButton(menuFrame, "ESP", 100, settings.ESP, function(state)
        settings.ESP = state
        if not state then
            -- Remover todos os ESPs
            for _, v in pairs(guiObjects) do
                if v:IsA("Drawing") then
                    v:Remove()
                end
            end
            table.clear(guiObjects)
        end
    end)
    
    createToggleButton(menuFrame, "Team Check", 150, settings.TeamCheck, function(state)
        settings.TeamCheck = state
    end)
    
    -- ===== SELECTOR DE TIPO ESP =====
    local espTypeFrame = Instance.new("Frame")
    espTypeFrame.Size = UDim2.new(0.9, 0, 0, 40)
    espTypeFrame.Position = UDim2.new(0.05, 0, 0, 200)
    espTypeFrame.BackgroundColor3 = CONFIG.BUTTON_COLOR
    espTypeFrame.Parent = menuFrame
    
    local espTypeCorner = Instance.new("UICorner")
    espTypeCorner.CornerRadius = UDim.new(0, 5)
    espTypeCorner.Parent = espTypeFrame
    
    local espTypeLabel = Instance.new("TextLabel")
    espTypeLabel.Size = UDim2.new(0.4, 0, 1, 0)
    espTypeLabel.Position = UDim2.new(0, 10, 0, 0)
    espTypeLabel.BackgroundTransparency = 1
    espTypeLabel.Text = "ESP Type:"
    espTypeLabel.TextColor3 = CONFIG.TEXT_COLOR
    espTypeLabel.TextXAlignment = Enum.TextXAlignment.Left
    espTypeLabel.Font = Enum.Font.Gotham
    espTypeLabel.TextScaled = true
    espTypeLabel.Parent = espTypeFrame
    
    local espTypeDropdown = Instance.new("TextButton")
    espTypeDropdown.Size = UDim2.new(0.5, 0, 0.7, 0)
    espTypeDropdown.Position = UDim2.new(0.45, 0, 0.15, 0)
    espTypeDropdown.BackgroundColor3 = Color3.fromRGB(60, 60, 65)
    espTypeDropdown.Text = "Box"
    espTypeDropdown.TextColor3 = CONFIG.TEXT_COLOR
    espTypeDropdown.Font = Enum.Font.Gotham
    espTypeDropdown.TextScaled = true
    espTypeDropdown.Parent = espTypeFrame
    
    local dropdownCorner = Instance.new("UICorner")
    dropdownCorner.CornerRadius = UDim.new(0, 5)
    dropdownCorner.Parent = espTypeDropdown
    
    -- Alternar tipos de ESP
    local espTypes = {"Box", "Skeleton", "Line"}
    local currentType = 1
    espTypeDropdown.MouseButton1Click:Connect(function()
        currentType = currentType % #espTypes + 1
        espTypeDropdown.Text = espTypes[currentType]
        settings.ESPType = espTypes[currentType]
    end)
    
    -- ===== CONTROLE DO FOV =====
    local fovControlFrame = Instance.new("Frame")
    fovControlFrame.Size = UDim2.new(0.9, 0, 0, 60)
    fovControlFrame.Position = UDim2.new(0.05, 0, 0, 250)
    fovControlFrame.BackgroundColor3 = CONFIG.BUTTON_COLOR
    fovControlFrame.Parent = menuFrame
    
    local fovControlCorner = Instance.new("UICorner")
    fovControlCorner.CornerRadius = UDim.new(0, 5)
    fovControlCorner.Parent = fovControlFrame
    
    local fovLabel = Instance.new("TextLabel")
    fovLabel.Size = UDim2.new(1, 0, 0.4, 0)
    fovLabel.Position = UDim2.new(0, 0, 0, 5)
    fovLabel.BackgroundTransparency = 1
    fovLabel.Text = "FOV Size: " .. settings.FOVSize
    fovLabel.TextColor3 = CONFIG.TEXT_COLOR
    fovLabel.Font = Enum.Font.Gotham
    fovLabel.TextScaled = true
    fovLabel.Parent = fovControlFrame
    
    local fovSlider = Instance.new("TextBox")
    fovSlider.Size = UDim2.new(0.8, 0, 0.4, 0)
    fovSlider.Position = UDim2.new(0.1, 0, 0.5, 0)
    fovSlider.BackgroundColor3 = Color3.fromRGB(60, 60, 65)
    fovSlider.Text = tostring(settings.FOVSize)
    fovSlider.TextColor3 = CONFIG.TEXT_COLOR
    fovSlider.Font = Enum.Font.Gotham
    fovSlider.TextScaled = true
    fovSlider.Parent = fovControlFrame
    
    local sliderCorner = Instance.new("UICorner")
    sliderCorner.CornerRadius = UDim.new(0, 5)
    sliderCorner.Parent = fovSlider
    
    fovSlider.FocusLost:Connect(function()
        local value = tonumber(fovSlider.Text)
        if value then
            settings.FOVSize = math.clamp(value, 50, 500)
            fovLabel.Text = "FOV Size: " .. settings.FOVSize
            fovSlider.Text = tostring(settings.FOVSize)
        else
            fovSlider.Text = tostring(settings.FOVSize)
        end
    end)
    
    -- ===== BOTÃO ABRIR/FECHAR MENU COM LOGO EDGARHUB =====
    local toggleMenuButton = Instance.new("TextButton")
    toggleMenuButton.Name = "ToggleMenuButton"
    toggleMenuButton.Size = UDim2.new(0, 70, 0, 70)  -- Um pouco maior para acomodar o logo
    toggleMenuButton.Position = UDim2.new(0, 20, 0.9, -80)
    toggleMenuButton.BackgroundColor3 = CONFIG.BUTTON_COLOR
    toggleMenuButton.Text = ""
    toggleMenuButton.TextColor3 = CONFIG.TEXT_COLOR
    toggleMenuButton.Font = Enum.Font.GothamBold
    toggleMenuButton.Parent = screenGui
    
    local toggleButtonCorner = Instance.new("UICorner")
    toggleButtonCorner.CornerRadius = UDim.new(0, 15)  -- Mais arredondado
    toggleButtonCorner.Parent = toggleMenuButton
    
    -- Frame interno para organizar o logo
    local logoFrame = Instance.new("Frame")
    logoFrame.Size = UDim2.new(1, -10, 1, -10)
    logoFrame.Position = UDim2.new(0, 5, 0, 5)
    logoFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 45)
    logoFrame.BackgroundTransparency = 0.3
    logoFrame.BorderSizePixel = 0
    logoFrame.Parent = toggleMenuButton
    
    local logoCorner = Instance.new("UICorner")
    logoCorner.CornerRadius = UDim.new(0, 10)
    logoCorner.Parent = logoFrame
    
    -- Letra E vermelha grande no botão
    local buttonE = Instance.new("TextLabel")
    buttonE.Size = UDim2.new(0.5, 0, 1, 0)
    buttonE.Position = UDim2.new(0, 5, 0, 0)
    buttonE.BackgroundTransparency = 1
    buttonE.Text = "E"
    buttonE.TextColor3 = CONFIG.EDGAR_RED
    buttonE.TextScaled = true
    buttonE.Font = Enum.Font.GothamBold
    buttonE.TextXAlignment = Enum.TextXAlignment.Right
    buttonE.Parent = logoFrame
    
    -- Texto "hub" pequeno
    local buttonHub = Instance.new("TextLabel")
    buttonHub.Size = UDim2.new(0.5, 0, 0.6, 0)
    buttonHub.Position = UDim2.new(0.5, -5, 0.2, 0)
    buttonHub.BackgroundTransparency = 1
    buttonHub.Text = "hub"
    buttonHub.TextColor3 = CONFIG.TEXT_COLOR
    buttonHub.TextScaled = true
    buttonHub.Font = Enum.Font.Gotham
    buttonHub.TextXAlignment = Enum.TextXAlignment.Left
    buttonHub.Parent = logoFrame
    
    -- Linha decorativa
    local decorLine = Instance.new("Frame")
    decorLine.Size = UDim2.new(0.8, 0, 0, 2)
    decorLine.Position = UDim2.new(0.1, 0, 0.8, 0)
    decorLine.BackgroundColor3 = CONFIG.EDGAR_RED
    decorLine.BorderSizePixel = 0
    decorLine.Parent = logoFrame
    
    local lineCorner = Instance.new("UICorner")
    lineCorner.CornerRadius = UDim.new(0, 1)
    lineCorner.Parent = decorLine
    
    toggleMenuButton.MouseButton1Click:Connect(function()
        menuFrame.Visible = not menuFrame.Visible
    end)
    
    return screenGui
end

-- ========== CRIAÇÃO DO FOV VISUAL ==========
local function createFOVCircle()
    local fovCircle = Instance.new("Frame")
    fovCircle.Name = "FOVCircle"
    fovCircle.Size = UDim2.new(0, settings.FOVSize * 2, 0, settings.FOVSize * 2)
    fovCircle.Position = UDim2.new(0.5, -settings.FOVSize, 0.5, -settings.FOVSize)
    fovCircle.BackgroundColor3 = CONFIG.FOV_COLOR
    fovCircle.BackgroundTransparency = 0.9
    fovCircle.BorderSizePixel = 2
    fovCircle.BorderColor3 = CONFIG.FOV_COLOR
    fovCircle.Visible = true
    fovCircle.Parent = LocalPlayer:WaitForChild("PlayerGui")
    
    local fovCorner = Instance.new("UICorner")
    fovCorner.CornerRadius = UDim.new(1, 0)
    fovCorner.Parent = fovCircle
    
    return fovCircle
end

-- ========== VERIFICAÇÕES DE TIME E VISIBILIDADE ==========
local function isEnemy(player)
    if player == LocalPlayer then return false end
    if not settings.TeamCheck then return true end
    
    local localTeam = LocalPlayer.Team
    local playerTeam = player.Team
    
    if localTeam and playerTeam then
        return localTeam ~= playerTeam
    end
    return true
end

local function isVisible(targetPart)
    if not targetPart then return false end
    
    local origin = Camera.CFrame.Position
    local target = targetPart.Position
    local direction = (target - origin).Unit
    local raycastParams = RaycastParams.new()
    raycastParams.FilterDescendantsInstances = {LocalPlayer.Character, targetPart.Parent}
    raycastParams.FilterType = Enum.RaycastFilterType.Blacklist
    
    local rayResult = workspace:Raycast(origin, direction * CONFIG.MAX_DISTANCE, raycastParams)
    return rayResult == nil or rayResult.Instance:IsDescendantOf(targetPart.Parent)
end

-- ========== SISTEMA DE SELEÇÃO DE ALVO ==========
local function getClosestTarget()
    local center = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
    local closestTarget = nil
    local closestDistance = settings.FOVSize
    
    for _, player in ipairs(Players:GetPlayers()) do
        if isEnemy(player) and player.Character and player.Character:FindFirstChild("Humanoid") and player.Character.Humanoid.Health > 0 then
            local head = player.Character:FindFirstChild("Head")
            if head then
                local headPos, onScreen = Camera:WorldToViewportPoint(head.Position)
                if onScreen then
                    local headVector = Vector2.new(headPos.X, headPos.Y)
                    local distanceFromCenter = (headVector - center).Magnitude
                    
                    if distanceFromCenter <= closestDistance and isVisible(head) then
                        closestDistance = distanceFromCenter
                        closestTarget = head
                    end
                end
            end
        end
    end
    
    return closestTarget
end

-- ========== SISTEMA DE ESP ==========
local function drawESP()
    -- Limpar ESPs antigos
    for _, v in pairs(guiObjects) do
        if v:IsA("Drawing") then
            v:Remove()
        end
    end
    table.clear(guiObjects)
    
    if not settings.ESP then return end
    
    for _, player in ipairs(Players:GetPlayers()) do
        if isEnemy(player) and player.Character and player.Character:FindFirstChild("Humanoid") and player.Character.Humanoid.Health > 0 then
            local rootPart = player.Character:FindFirstChild("HumanoidRootPart")
            local head = player.Character:FindFirstChild("Head")
            
            if rootPart and head then
                local rootPos, onScreen = Camera:WorldToViewportPoint(rootPart.Position)
                local headPos, _ = Camera:WorldToViewportPoint(head.Position)
                
                if onScreen then
                    if settings.ESPType == "Box" then
                        -- Desenhar caixa 2D
                        local box = Drawing.new("Square")
                        box.Visible = true
                        box.Color = CONFIG.ESP_BOX_COLOR
                        box.Thickness = 2
                        box.Transparency = 1
                        box.Filled = false
                        
                        local height = math.abs(headPos.Y - rootPos.Y) * 1.5
                        local width = height * 0.6
                        
                        box.Position = Vector2.new(rootPos.X - width/2, rootPos.Y - height/2)
                        box.Size = Vector2.new(width, height)
                        
                        table.insert(guiObjects, box)
                        
                    elseif settings.ESPType == "Skeleton" then
                        -- Desenhar esqueleto simplificado
                        local parts = {"Head", "Torso", "Left Arm", "Right Arm", "Left Leg", "Right Leg"}
                        local connections = {
                            {"Head", "Torso"},
                            {"Torso", "Left Arm"},
                            {"Torso", "Right Arm"},
                            {"Torso", "Left Leg"},
                            {"Torso", "Right Leg"}
                        }
                        
                        for _, connection in ipairs(connections) do
                            local part1 = player.Character:FindFirstChild(connection[1])
                            local part2 = player.Character:FindFirstChild(connection[2])
                            
                            if part1 and part2 then
                                local pos1, _ = Camera:WorldToViewportPoint(part1.Position)
                                local pos2, _ = Camera:WorldToViewportPoint(part2.Position)
                                
                                local line = Drawing.new("Line")
                                line.Visible = true
                                line.Color = CONFIG.ESP_SKELETON_COLOR
                                line.Thickness = 2
                                line.Transparency = 1
                                line.From = Vector2.new(pos1.X, pos1.Y)
                                line.To = Vector2.new(pos2.X, pos2.Y)
                                
                                table.insert(guiObjects, line)
                            end
                        end
                        
                    elseif settings.ESPType == "Line" then
                        -- Desenhar linha até o jogador
                        local line = Drawing.new("Line")
                        line.Visible = true
                        line.Color = CONFIG.ESP_LINE_COLOR
                        line.Thickness = 2
                        line.Transparency = 1
                        line.From = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y)
                        line.To = Vector2.new(rootPo
