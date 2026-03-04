local Lighting = game:GetService("Lighting")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local CoreGui = game:GetService("CoreGui")
local LocalPlayer = Players.LocalPlayer

local CONFIG = {
    BRIGHTNESS = 5,
    AMBIENT = 4,
    AMBIENT_COLOR = Color3.new(1, 1, 1),
    SHADOWS = false,
    TEXTURES = false,
    ANIMATIONS = false,
    FOG = false,
    WATER = false,
    PARTICLES = false
}

local settings = {
    Brightness = false,
    NoShadows = false,
    NoTextures = false,
    NoAnimations = false,
    NoFog = false,
    NoWater = false,
    NoParticles = false
}

local originalLighting = {
    Brightness = Lighting.Brightness,
    Ambient = Lighting.Ambient,
    GlobalShadows = Lighting.GlobalShadows,
    FogColor = Lighting.FogColor,
    FogEnd = Lighting.FogEnd,
    FogStart = Lighting.FogStart
}

local connections = {}
local guiElements = {}
local processedInstances = {}

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "DeltaHub"
screenGui.ResetOnSpawn = false
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
screenGui.Parent = CoreGui

local menuFrame = Instance.new("Frame")
menuFrame.Name = "MainMenu"
menuFrame.Size = UDim2.new(0, 220, 0, 320)
menuFrame.Position = UDim2.new(0, 50, 0.5, -160)
menuFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
menuFrame.BackgroundTransparency = 0.1
menuFrame.BorderSizePixel = 0
menuFrame.Active = true
menuFrame.Draggable = true
menuFrame.Parent = screenGui

local menuCorner = Instance.new("UICorner")
menuCorner.CornerRadius = UDim.new(0, 12)
menuCorner.Parent = menuFrame

local menuStroke = Instance.new("UIStroke")
menuStroke.Color = Color3.fromRGB(0, 200, 255)
menuStroke.Thickness = 2
menuStroke.Transparency = 0.5
menuStroke.Parent = menuFrame

local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1, 0, 0, 35)
titleBar.BackgroundColor3 = Color3.fromRGB(0, 150, 200)
titleBar.BackgroundTransparency = 0.2
titleBar.BorderSizePixel = 0
titleBar.Parent = menuFrame

local titleCorner = Instance.new("UICorner")
titleCorner.CornerRadius = UDim.new(0, 12)
titleCorner.Parent = titleBar

local titleLabel = Instance.new("TextLabel")
titleLabel.Size = UDim2.new(1, -10, 1, 0)
titleLabel.Position = UDim2.new(0, 10, 0, 0)
titleLabel.BackgroundTransparency = 1
titleLabel.Text = "⚡ DELTA HUB"
titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
titleLabel.TextXAlignment = Enum.TextXAlignment.Left
titleLabel.Font = Enum.Font.GothamBold
titleLabel.TextSize = 16
titleLabel.Parent = titleBar

local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0, 25, 0, 25)
closeButton.Position = UDim2.new(1, -30, 0, 5)
closeButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
closeButton.Text = "✕"
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.TextSize = 16
closeButton.Font = Enum.Font.GothamBold
closeButton.Parent = titleBar

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0, 6)
closeCorner.Parent = closeButton

closeButton.MouseButton1Click:Connect(function()
    menuFrame.Visible = false
end)

local function createToggle(parent, name, yPos, setting)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0.9, 0, 0, 35)
    frame.Position = UDim2.new(0.05, 0, 0, yPos)
    frame.BackgroundColor3 = Color3.fromRGB(35, 35, 40)
    frame.BackgroundTransparency = 0.2
    frame.Parent = parent
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 8)
    corner.Parent = frame
    
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0.6, 0, 1, 0)
    label.Position = UDim2.new(0, 10, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = name
    label.TextColor3 = Color3.fromRGB(220, 220, 220)
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Font = Enum.Font.Gotham
    label.TextSize = 14
    label.Parent = frame
    
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(0.3, 0, 0.7, 0)
    button.Position = UDim2.new(0.65, 0, 0.15, 0)
    button.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
    button.Text = "OFF"
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.TextSize = 12
    button.Font = Enum.Font.GothamBold
    button.Parent = frame
    
    local buttonCorner = Instance.new("UICorner")
    buttonCorner.CornerRadius = UDim.new(0, 5)
    buttonCorner.Parent = button
    
    button.MouseButton1Click:Connect(function()
        local newState = not settings[setting]
        settings[setting] = newState
        
        if newState then
            button.BackgroundColor3 = Color3.fromRGB(50, 255, 50)
            button.Text = "ON"
        else
            button.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
            button.Text = "OFF"
        end
        
        if setting == "Brightness" then
            if newState then
                Lighting.Brightness = CONFIG.BRIGHTNESS
                Lighting.Ambient = CONFIG.AMBIENT_COLOR * CONFIG.AMBIENT
                Lighting.OutdoorAmbient = CONFIG.AMBIENT_COLOR * (CONFIG.AMBIENT * 0.8)
                Lighting.ColorShift_Top = Color3.new(1, 1, 1)
                Lighting.ColorShift_Bottom = Color3.new(0.5, 0.5, 0.5)
            else
                Lighting.Brightness = originalLighting.Brightness
                Lighting.Ambient = originalLighting.Ambient
            end
            
        elseif setting == "NoShadows" then
            Lighting.GlobalShadows = not newState
            Lighting.ShadowSoftness = newState and 0 or 0.5
            
        elseif setting == "NoTextures" then
            local function processTextures(obj)
                if processedInstances[obj] then return end
                processedInstances[obj] = true
                
                if obj:IsA("Part") or obj:IsA("MeshPart") or obj:IsA("UnionOperation") then
                    if newState then
                        obj.Material = Enum.Material.SmoothPlastic
                        obj.Reflectance = 0
                    end
                elseif obj:IsA("Decal") or obj:IsA("Texture") then
                    obj.Transparency = newState and 1 or 0
                elseif obj:IsA("Sky") then
                    obj.Parent = newState and nil or Lighting
                end
                
                for _, child in ipairs(obj:GetChildren()) do
                    processTextures(child)
                end
            end
            
            processTextures(workspace)
            processTextures(Lighting)
            
        elseif setting == "NoAnimations" then
            local function processCharacter(char)
                if not char then return end
                
                local humanoid = char:FindFirstChildOfClass("Humanoid")
                if humanoid then
                    local animator = humanoid:FindFirstChildOfClass("Animator")
                    if animator and newState then
                        for _, track in ipairs(animator:GetPlayingAnimationTracks()) do
                            track:Stop()
                        end
                        animator:ClearAllChildren()
                    end
                end
                
                local animate = char:FindFirstChild("Animate")
                if animate and animate:IsA("LocalScript") then
                    animate.Disabled = newState
                end
            end
            
            for _, player in ipairs(Players:GetPlayers()) do
                if player.Character then
                    processCharacter(player.Character)
                end
            end
            
        elseif setting == "NoFog" then
            if newState then
                Lighting.FogEnd = 1e9
                Lighting.FogStart = 0
                Lighting.FogColor = Color3.new(0, 0, 0)
            else
                Lighting.FogEnd = originalLighting.FogEnd
                Lighting.FogStart = originalLighting.FogStart
                Lighting.FogColor = originalLighting.FogColor
            end
            
        elseif setting == "NoWater" then
            local terrain = workspace:FindFirstChildOfClass("Terrain")
            if terrain then
                terrain.WaterTransparency = newState and 1 or 0.5
                terrain.WaterReflectance = newState and 0 or 0.5
                terrain.WaterWaveSize = newState and 0 or 0.5
                terrain.WaterWaveSpeed = newState and 0 or 10
            end
            
        elseif setting == "NoParticles" then
            local function processParticles(obj)
                if obj:IsA("ParticleEmitter") or obj:IsA("Smoke") or obj:IsA("Fire") or obj:IsA("Sparkles") then
                    obj.Enabled = not newState
                elseif obj:IsA("Trail") then
                    obj.Enabled = not newState
                end
                
                for _, child in ipairs(obj:GetChildren()) do
                    processParticles(child)
                end
            end
            
            processParticles(workspace)
        end
    end)
    
    return frame
end

createToggle(menuFrame, "☀️ Brilho Máximo", 45, "Brightness")
createToggle(menuFrame, "🌑 Sem Sombras", 85, "NoShadows")
createToggle(menuFrame, "🎨 Sem Texturas", 125, "NoTextures")
createToggle(menuFrame, "🔄 Sem Animações", 165, "NoAnimations")
createToggle(menuFrame, "🌫️ Sem Neblina", 205, "NoFog")
createToggle(menuFrame, "💧 Sem Água", 245, "NoWater")
createToggle(menuFrame, "✨ Sem Partículas", 285, "NoParticles")

local toggleButton = Instance.new("TextButton")
toggleButton.Size = UDim2.new(0, 45, 0, 45)
toggleButton.Position = UDim2.new(0, 20, 0.9, -60)
toggleButton.BackgroundColor3 = Color3.fromRGB(0, 150, 200)
toggleButton.Text = "⚡"
toggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleButton.TextSize = 24
toggleButton.Font = Enum.Font.GothamBold
toggleButton.Parent = screenGui

local toggleCorner = Instance.new("UICorner")
toggleCorner.CornerRadius = UDim.new(0, 12)
toggleCorner.Parent = toggleButton

local toggleStroke = Instance.new("UIStroke")
toggleStroke.Color = Color3.fromRGB(255, 255, 255)
toggleStroke.Thickness = 2
toggleStroke.Transparency = 0.3
toggleStroke.Parent = toggleButton

toggleButton.MouseButton1Click:Connect(function()
    menuFrame.Visible = not menuFrame.Visible
end)

UserInputService.InputBegan:Connect(function(input)
    if input.KeyCode == Enum.KeyCode.Insert then
        menuFrame.Visible = not menuFrame.Visible
    elseif input.KeyCode == Enum.KeyCode.RightControl then
        toggleButton.Visible = not toggleButton.Visible
    end
end)

local fpsLabel = Instance.new("TextLabel")
fpsLabel.Size = UDim2.new(0, 70, 0, 25)
fpsLabel.Position = UDim2.new(1, -80, 0, 10)
fpsLabel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
fpsLabel.BackgroundTransparency = 0.3
fpsLabel.Text = "FPS: 60"
fpsLabel.TextColor3 = Color3.fromRGB(0, 255, 100)
fpsLabel.TextSize = 14
fpsLabel.Font = Enum.Font.GothamBold
fpsLabel.Parent = screenGui

local fpsCorner = Instance.new("UICorner")
fpsCorner.CornerRadius = UDim.new(0, 8)
fpsCorner.Parent = fpsLabel

local lastTime = tick()
local frames = 0

RunService.RenderStepped:Connect(function()
    frames = frames + 1
    local currentTime = tick()
    if currentTime - lastTime >= 1 then
        fpsLabel.Text = "FPS: " .. math.floor(frames / (currentTime - lastTime))
        frames = 0
        lastTime = currentTime
    end
end)

for _, player in ipairs(Players:GetPlayers()) do
    if player ~= LocalPlayer and player.Character then
        player.CharacterAdded:Connect(function(char)
            task.wait(0.5)
            if settings.NoAnimations and char then
                local humanoid = char:FindFirstChildOfClass("Humanoid")
                if humanoid then
                    local animator = humanoid:FindFirstChildOfClass("Animator")
                    if animator then
                        for _, track in ipairs(animator:GetPlayingAnimationTracks()) do
                            track:Stop()
                        end
                    end
                end
            end
        end)
    end
end

Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(char)
        task.wait(0.5)
        if settings.NoAnimations and char then
            local humanoid = char:FindFirstChildOfClass("Humanoid")
            if humanoid then
                local animator = humanoid:FindFirstChildOfClass("Animator")
                if animator then
                    for _, track in ipairs(animator:GetPlayingAnimationTracks()) do
                        track:Stop()
                    end
                end
            end
        end
    end)
end

local terrain = workspace:FindFirstChildOfClass("Terrain")
if terrain then
    terrain.WaterTransparency = settings.NoWater and 1 or 0.5
end

LocalPlayer.OnTeleport:Connect(function()
    for _, conn in ipairs(connections) do
        conn:Disconnect()
    end
    screenGui:Destroy()
end)
