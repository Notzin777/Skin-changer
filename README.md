--[[
NotzinESPMenuV5.lua
- Menu ESP ultra customizado (Main, Esp, Info)
- Sliders: Speed, Jump Power
- Botões: Invisível, Fly (ambos com keybind configurável, funcionam mesmo com menu fechado)
- ESP Highlight: cor e keybind configurável
- CHAMS: jogadores sempre visíveis pelas paredes (local/visual)
- Menu arredondado, abas laterais, abre/fecha com P
- Fly na aba Main, velocidade fixa (mude FLY_SPEED se quiser)
- Skin Changer: Headless, Korblox, A Vingança do Alce (e reset de cada um)
]]

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- ====== FLY VARIÁVEIS ======
local FLY_ENABLED = false
local FLY_KEY = Enum.KeyCode.G
local FLY_SPEED = 60
local flyConn = nil
local waitingFlyKey = false

local function setFly(state)
    FLY_ENABLED = state
    local char = LocalPlayer.Character
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    if hum then
        hum.PlatformStand = state
    end
    if state then
        if flyConn then flyConn:Disconnect() end
        flyConn = RunService.RenderStepped:Connect(function()
            local char = LocalPlayer.Character
            if not char or not char:FindFirstChild("HumanoidRootPart") then return end
            local HRP = char.HumanoidRootPart
            local cam = workspace.CurrentCamera
            local moveVec = Vector3.new()
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveVec = moveVec + cam.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveVec = moveVec - cam.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveVec = moveVec - cam.CFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveVec = moveVec + cam.CFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then moveVec = moveVec + cam.CFrame.UpVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then moveVec = moveVec - cam.CFrame.UpVector end
            if moveVec.Magnitude > 0 then
                HRP.Velocity = moveVec.Unit * FLY_SPEED
            else
                HRP.Velocity = Vector3.new(0,0,0)
            end
        end)
    else
        if flyConn then flyConn:Disconnect() flyConn = nil end
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
            LocalPlayer.Character.Humanoid.PlatformStand = false
            LocalPlayer.Character.HumanoidRootPart.Velocity = Vector3.new(0,0,0)
        end
    end
end
-- ====== /FLY ======

-- ====== INVISÍVEL ======
local INVIS_ENABLED = false
local INVIS_KEY = Enum.KeyCode.T
local invisParts = {}
local waitingInvisKey = false

local function setInvisible(state)
    INVIS_ENABLED = state
    local char = LocalPlayer.Character
    if char then
        for _,part in ipairs(char:GetDescendants()) do
            if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                if state then
                    if not invisParts[part] then
                        invisParts[part] = part.Transparency
                    end
                    part.Transparency = 1
                    part.CanCollide = false
                else
                    if invisParts[part] then
                        part.Transparency = invisParts[part]
                        invisParts[part] = nil
                    end
                    part.CanCollide = true
                end
            elseif part:IsA("Decal") then
                part.Transparency = state and 1 or 0
            end
        end
        -- Esconde acessórios
        for _, acc in ipairs(char:GetChildren()) do
            if acc:IsA("Accessory") and acc:FindFirstChild("Handle") then
                acc.Handle.Transparency = state and 1 or 0
            end
        end
    end
end
-- ====== /INVISÍVEL ======

-- ====== ESP VARS ======
local ESP_ENABLED = false
local HIGHLIGHT_NAME = "PurpleESP_Highlight"
local toggleKey = Enum.KeyCode.F -- Tecla padrão ESP
local waitingForKey = false
local highlightColor = "Roxo"
local highlightColors = {
    ["Roxo"] = {fill = Color3.fromRGB(128,0,128), outline = Color3.fromRGB(200,0,200)},
    ["Amarelo"] = {fill = Color3.fromRGB(255,255,0), outline = Color3.fromRGB(255,255,90)},
    ["Verde"] = {fill = Color3.fromRGB(0,255,0), outline = Color3.fromRGB(90,255,90)},
    ["Azul"] = {fill = Color3.fromRGB(0,128,255), outline = Color3.fromRGB(0,200,255)},
}
local fillTransparency = 0.08
local outlineTransparency = 0.0

local function setESPState(state)
    ESP_ENABLED = state
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            local existing = player.Character:FindFirstChild(HIGHLIGHT_NAME)
            if state then
                if not existing then
                    local highlight = Instance.new("Highlight")
                    highlight.Name = HIGHLIGHT_NAME
                    local color = highlightColors[highlightColor]
                    highlight.FillColor = color.fill
                    highlight.OutlineColor = color.outline
                    highlight.FillTransparency = fillTransparency
                    highlight.OutlineTransparency = outlineTransparency
                    highlight.Adornee = player.Character
                    highlight.Parent = player.Character
                end
            else
                if existing then
                    existing:Destroy()
                end
            end
        end
    end
end

local function updateHighlightColors()
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            local h = player.Character:FindFirstChild(HIGHLIGHT_NAME)
            if h and h:IsA("Highlight") then
                local color = highlightColors[highlightColor]
                h.FillColor = color.fill
                h.OutlineColor = color.outline
                h.FillTransparency = fillTransparency
                h.OutlineTransparency = outlineTransparency
            end
        end
    end
end

Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(char)
        if ESP_ENABLED then
            local highlight = Instance.new("Highlight")
            highlight.Name = HIGHLIGHT_NAME
            local color = highlightColors[highlightColor]
            highlight.FillColor = color.fill
            highlight.OutlineColor = color.outline
            highlight.FillTransparency = fillTransparency
            highlight.OutlineTransparency = outlineTransparency
            highlight.Adornee = char
            highlight.Parent = char
        end
    end)
end)
-- ====== /ESP ======

-- ====== CHAMS VARS ======
local CHAMS_ENABLED = false
local chamsFolder = Instance.new("Folder")
chamsFolder.Name = "ChamsFolder"
chamsFolder.Parent = workspace

local chamsColor = Color3.fromRGB(0, 255, 255)
local chamsTransparency = 0.4

local function setChams(state)
    CHAMS_ENABLED = state
    -- Remove chams antigos
    for _, obj in ipairs(chamsFolder:GetChildren()) do
        obj:Destroy()
    end
    if state then
        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= LocalPlayer and player.Character then
                for _, part in ipairs(player.Character:GetDescendants()) do
                    if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                        local cham = Instance.new("BoxHandleAdornment")
                        cham.Adornee = part
                        cham.Size = part.Size + Vector3.new(0.01,0.01,0.01)
                        cham.AlwaysOnTop = true
                        cham.ZIndex = 10
                        cham.Color3 = chamsColor
                        cham.Transparency = chamsTransparency
                        cham.Parent = chamsFolder
                    end
                end
            end
        end
    end
end

Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        if CHAMS_ENABLED then
            setChams(true)
        end
    end)
end)
Players.PlayerRemoving:Connect(function()
    if CHAMS_ENABLED then
        setChams(true)
    end
end)
RunService.RenderStepped:Connect(function()
    if CHAMS_ENABLED then
        setChams(true)
    end
end)
-- ====== /CHAMS ======

-- ====== SPEED/JUMP ======
local walkSpeed = 16
local jumpPower = 50
local maxSpeed = 200
local maxJump = 400

LocalPlayer.CharacterAdded:Connect(function(char)
    local hum = char:WaitForChild("Humanoid")
    hum.WalkSpeed = walkSpeed
    hum.JumpPower = jumpPower
end)
-- ====== /SPEED/JUMP ======

-- ========== GUI ==========
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "NotzinESPMenu"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 430, 0, 370)
MainFrame.Position = UDim2.new(0.5, -215, 0.4, -185)
MainFrame.BackgroundColor3 = Color3.fromRGB(25,25,32)
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.ZIndex = 2
MainFrame.Parent = ScreenGui

local UICorner_Main = Instance.new("UICorner")
UICorner_Main.CornerRadius = UDim.new(0, 18)
UICorner_Main.Parent = MainFrame

-- Título
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 36)
Title.Position = UDim2.new(0, 0, 0, 0)
Title.BackgroundTransparency = 1
Title.Font = Enum.Font.GothamBold
Title.Text = "Notzin"
Title.TextSize = 28
Title.TextColor3 = Color3.fromRGB(170, 85, 255)
Title.Parent = MainFrame
Title.ZIndex = 2

-- Barra lateral de abas
local sideBar = Instance.new("Frame")
sideBar.Size = UDim2.new(0, 92, 1, -36)
sideBar.Position = UDim2.new(0,0,0,36)
sideBar.BackgroundColor3 = Color3.fromRGB(30,30,45)
sideBar.BorderSizePixel = 0
sideBar.ZIndex = 2
sideBar.Parent = MainFrame

local UICorner_sideBar = Instance.new("UICorner")
UICorner_sideBar.CornerRadius = UDim.new(0, 14)
UICorner_sideBar.Parent = sideBar

local tabNames = {"Main", "Esp", "Info"}
local tabButtons = {}
local tabContents = {}

local function showTab(idx)
    for i, c in ipairs(tabContents) do
        c.Visible = (i == idx)
        tabButtons[i].BackgroundColor3 = (i == idx) and Color3.fromRGB(80, 60, 100) or Color3.fromRGB(30, 30, 45)
    end
end

for i, name in ipairs(tabNames) do
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -16, 0, 38)
    btn.Position = UDim2.new(0, 8, 0, 12 + (i-1)*46)
    btn.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
    btn.Font = Enum.Font.GothamSemibold
    btn.Text = name
    btn.TextSize = 20
    btn.TextColor3 = Color3.fromRGB(230, 230, 255)
    btn.AutoButtonColor = true
    btn.ZIndex = 2
    btn.Parent = sideBar
    local UICorner_btn = Instance.new("UICorner")
    UICorner_btn.CornerRadius = UDim.new(0, 8)
    UICorner_btn.Parent = btn
    btn.MouseButton1Click:Connect(function() showTab(i) end)
    tabButtons[i] = btn

    local content = Instance.new("Frame")
    content.Size = UDim2.new(1,-110,1,-42)
    content.Position = UDim2.new(0, 102, 0, 40)
    content.BackgroundTransparency = 1
    content.Visible = false
    content.ZIndex = 2
    content.Parent = MainFrame
    tabContents[i] = content
end

---------------- MAIN TAB ----------------
local mainTab = tabContents[1]

local function createSlider(labelText, minValue, maxValue, startValue, posY, callback, valueColor)
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0, 120, 0, 24)
    label.Position = UDim2.new(0, 10, 0, posY)
    label.BackgroundTransparency = 1
    label.Font = Enum.Font.Gotham
    label.Text = labelText .. ":"
    label.TextSize = 17
    label.TextColor3 = Color3.fromRGB(220,220,255)
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.ZIndex = 2
    label.Parent = mainTab

    local valueLabel = Instance.new("TextLabel")
    valueLabel.Size = UDim2.new(0, 54, 0, 24)
    valueLabel.Position = UDim2.new(0, 130, 0, posY)
    valueLabel.BackgroundTransparency = 1
    valueLabel.Font = Enum.Font.GothamBold
    valueLabel.Text = tostring(startValue)
    valueLabel.TextSize = 17
    valueLabel.TextColor3 = valueColor or Color3.fromRGB(120,255,120)
    valueLabel.TextXAlignment = Enum.TextXAlignment.Left
    valueLabel.ZIndex = 2
    valueLabel.Parent = mainTab

    local sliderBG = Instance.new("Frame")
    sliderBG.Size = UDim2.new(0, 220, 0, 12)
    sliderBG.Position = UDim2.new(0, 10, 0, posY+26)
    sliderBG.BackgroundColor3 = Color3.fromRGB(40,40,60)
    sliderBG.BorderSizePixel = 0
    sliderBG.ZIndex = 2
    sliderBG.Parent = mainTab
    local UICorner_BG = Instance.new("UICorner")
    UICorner_BG.CornerRadius = UDim.new(0, 6)
    UICorner_BG.Parent = sliderBG

    local sliderFill = Instance.new("Frame")
    sliderFill.Size = UDim2.new((startValue-minValue)/(maxValue-minValue),0,1,0)
    sliderFill.BackgroundColor3 = Color3.fromRGB(110,85,200)
    sliderFill.BorderSizePixel = 0
    sliderFill.ZIndex = 2
    sliderFill.Parent = sliderBG
    local UICorner_Fill = Instance.new("UICorner")
    UICorner_Fill.CornerRadius = UDim.new(0, 6)
    UICorner_Fill.Parent = sliderFill

    local dragging = false

    sliderBG.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
        end
    end)
    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local x = math.clamp(input.Position.X - sliderBG.AbsolutePosition.X, 0, sliderBG.AbsoluteSize.X)
            local pct = x/sliderBG.AbsoluteSize.X
            local value = math.floor(minValue + (maxValue-minValue)*pct + 0.5)
            sliderFill.Size = UDim2.new(pct,0,1,0)
            valueLabel.Text = tostring(value)
            callback(value)
        end
    end)
end

createSlider("Speed", 8, maxSpeed, walkSpeed, 10, function(val)
    walkSpeed = val
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = walkSpeed
    end
end, Color3.fromRGB(120,255,120))

createSlider("Jump Power", 20, maxJump, jumpPower, 60, function(val)
    jumpPower = val
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
        LocalPlayer.Character.Humanoid.JumpPower = jumpPower
    end
end, Color3.fromRGB(255,220,100))

-- Invisível
local invisBtn = Instance.new("TextButton")
invisBtn.Size = UDim2.new(0, 180, 0, 38)
invisBtn.Position = UDim2.new(0, 10, 0, 124)
invisBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 70)
invisBtn.Font = Enum.Font.GothamBold
invisBtn.TextSize = 20
invisBtn.TextColor3 = Color3.fromRGB(240, 255, 255)
invisBtn.AutoButtonColor = true
invisBtn.Text = "Ativar Invisível"
invisBtn.ZIndex = 2
invisBtn.Parent = mainTab
local UICorner_invis = Instance.new("UICorner")
UICorner_invis.CornerRadius = UDim.new(0, 8)
UICorner_invis.Parent = invisBtn

local function updateInvisBtn()
    invisBtn.Text = INVIS_ENABLED and "Desativar Invisível" or "Ativar Invisível"
    invisBtn.BackgroundColor3 = INVIS_ENABLED and Color3.fromRGB(80, 40, 120) or Color3.fromRGB(40, 40, 70)
end
invisBtn.MouseButton1Click:Connect(function()
    setInvisible(not INVIS_ENABLED)
    updateInvisBtn()
end)
updateInvisBtn()

local invisKeyLabel = Instance.new("TextLabel")
invisKeyLabel.Size = UDim2.new(0, 160, 0, 28)
invisKeyLabel.Position = UDim2.new(0, 10, 0, 170)
invisKeyLabel.BackgroundTransparency = 1
invisKeyLabel.Font = Enum.Font.Gotham
invisKeyLabel.Text = "Tecla Invisível:"
invisKeyLabel.TextSize = 18
invisKeyLabel.TextColor3 = Color3.fromRGB(230, 210, 255)
invisKeyLabel.ZIndex = 2
invisKeyLabel.Parent = mainTab

local invisKeyBtn = Instance.new("TextButton")
invisKeyBtn.Size = UDim2.new(0, 70, 0, 28)
invisKeyBtn.Position = UDim2.new(0, 172, 0, 170)
invisKeyBtn.BackgroundColor3 = Color3.fromRGB(40, 20, 60)
invisKeyBtn.Font = Enum.Font.GothamSemibold
invisKeyBtn.TextSize = 18
invisKeyBtn.TextColor3 = Color3.fromRGB(230, 210, 255)
invisKeyBtn.Text = INVIS_KEY.Name
invisKeyBtn.ZIndex = 2
invisKeyBtn.Parent = mainTab
local UICorner_invisKey = Instance.new("UICorner")
UICorner_invisKey.CornerRadius = UDim.new(0, 6)
UICorner_invisKey.Parent = invisKeyBtn

invisKeyBtn.MouseButton1Click:Connect(function()
    if waitingInvisKey then return end
    invisKeyBtn.Text = "Pressione..."
    waitingInvisKey = true
end)

-- FLY
local flyBtn = Instance.new("TextButton")
flyBtn.Size = UDim2.new(0, 180, 0, 38)
flyBtn.Position = UDim2.new(0, 10, 0, 210)
flyBtn.BackgroundColor3 = Color3.fromRGB(40, 60, 100)
flyBtn.Font = Enum.Font.GothamBold
flyBtn.TextSize = 20
flyBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
flyBtn.AutoButtonColor = true
flyBtn.Text = "Ativar Fly"
flyBtn.ZIndex = 2
flyBtn.Parent = mainTab
local UICorner_fly = Instance.new("UICorner")
UICorner_fly.CornerRadius = UDim.new(0, 8)
UICorner_fly.Parent = flyBtn

local function updateFlyBtn()
    flyBtn.Text = FLY_ENABLED and "Desativar Fly" or "Ativar Fly"
    flyBtn.BackgroundColor3 = FLY_ENABLED and Color3.fromRGB(80, 125, 180) or Color3.fromRGB(40, 60, 100)
end
flyBtn.MouseButton1Click:Connect(function()
    setFly(not FLY_ENABLED)
    updateFlyBtn()
end)
updateFlyBtn()

local flyKeyLabel = Instance.new("TextLabel")
flyKeyLabel.Size = UDim2.new(0, 160, 0, 28)
flyKeyLabel.Position = UDim2.new(0, 10, 0, 256)
flyKeyLabel.BackgroundTransparency = 1
flyKeyLabel.Font = Enum.Font.Gotham
flyKeyLabel.Text = "Tecla Fly:"
flyKeyLabel.TextSize = 18
flyKeyLabel.TextColor3 = Color3.fromRGB(230, 210, 255)
flyKeyLabel.ZIndex = 2
flyKeyLabel.Parent = mainTab

local flyKeyBtn = Instance.new("TextButton")
flyKeyBtn.Size = UDim2.new(0, 70, 0, 28)
flyKeyBtn.Position = UDim2.new(0, 172, 0, 256)
flyKeyBtn.BackgroundColor3 = Color3.fromRGB(40, 20, 60)
flyKeyBtn.Font = Enum.Font.GothamSemibold
flyKeyBtn.TextSize = 18
flyKeyBtn.TextColor3 = Color3.fromRGB(230, 210, 255)
flyKeyBtn.Text = FLY_KEY.Name
flyKeyBtn.ZIndex = 2
flyKeyBtn.Parent = mainTab
local UICorner_flyKey = Instance.new("UICorner")
UICorner_flyKey.CornerRadius = UDim.new(0, 6)
UICorner_flyKey.Parent = flyKeyBtn

flyKeyBtn.MouseButton1Click:Connect(function()
    if waitingFlyKey then return end
    flyKeyBtn.Text = "Pressione..."
    waitingFlyKey = true
end)

-- SKIN CHANGER: Headless, Korblox, Alce
local headlessBtn = Instance.new("TextButton")
headlessBtn.Size = UDim2.new(0, 180, 0, 36)
headlessBtn.Position = UDim2.new(0, 10, 0, 300)
headlessBtn.BackgroundColor3 = Color3.fromRGB(55, 40, 100)
headlessBtn.Font = Enum.Font.GothamBold
headlessBtn.TextSize = 18
headlessBtn.TextColor3 = Color3.fromRGB(255,255,255)
headlessBtn.Text = "Ativar Headless"
headlessBtn.AutoButtonColor = true
headlessBtn.ZIndex = 2
headlessBtn.Parent = mainTab
local UICorner_headless = Instance.new("UICorner")
UICorner_headless.CornerRadius = UDim.new(0, 8)
UICorner_headless.Parent = headlessBtn

local resetHeadlessBtn = Instance.new("TextButton")
resetHeadlessBtn.Size = UDim2.new(0, 180, 0, 28)
resetHeadlessBtn.Position = UDim2.new(0, 10, 0, 340)
resetHeadlessBtn.BackgroundColor3 = Color3.fromRGB(80, 30, 50)
resetHeadlessBtn.Font = Enum.Font.Gotham
resetHeadlessBtn.TextSize = 16
resetHeadlessBtn.TextColor3 = Color3.fromRGB(255, 220, 220)
resetHeadlessBtn.Text = "Reset Headless"
resetHeadlessBtn.AutoButtonColor = true
resetHeadlessBtn.ZIndex = 2
resetHeadlessBtn.Parent = mainTab
local UICorner_headlessReset = Instance.new("UICorner")
UICorner_headlessReset.CornerRadius = UDim.new(0, 8)
UICorner_headlessReset.Parent = resetHeadlessBtn

headlessBtn.MouseButton1Click:Connect(function()
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("Head") then
        char.Head.Transparency = 1
        for _,child in ipairs(char.Head:GetChildren()) do
            if child:IsA("Decal") or child:IsA("FaceInstance") then
                child.Transparency = 1
            end
        end
    end
end)
resetHeadlessBtn.MouseButton1Click:Connect(function()
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("Head") then
        char.Head.Transparency = 0
        for _,child in ipairs(char.Head:GetChildren()) do
            if child:IsA("Decal") or child:IsA("FaceInstance") then
                child.Transparency = 0
            end
        end
    end
end)

local korbloxBtn = Instance.new("TextButton")
korbloxBtn.Size = UDim2.new(0, 180, 0, 36)
korbloxBtn.Position = UDim2.new(0, 220, 0, 300)
korbloxBtn.BackgroundColor3 = Color3.fromRGB(55, 80, 120)
korbloxBtn.Font = Enum.Font.GothamBold
korbloxBtn.TextSize = 18
korbloxBtn.TextColor3 = Color3.fromRGB(255,255,255)
korbloxBtn.Text = "Ativar Korblox"
korbloxBtn.AutoButtonColor = true
korbloxBtn.ZIndex = 2
korbloxBtn.Parent = mainTab
local UICorner_korblox = Instance.new("UICorner")
UICorner_korblox.CornerRadius = UDim.new(0, 8)
UICorner_korblox.Parent = korbloxBtn

local resetKorbloxBtn = Instance.new("TextButton")
resetKorbloxBtn.Size = UDim2.new(0, 180, 0, 28)
resetKorbloxBtn.Position = UDim2.new(0, 220, 0, 340)
resetKorbloxBtn.BackgroundColor3 = Color3.fromRGB(80, 55, 100)
resetKorbloxBtn.Font = Enum.Font.Gotham
resetKorbloxBtn.TextSize = 16
resetKorbloxBtn.TextColor3 = Color3.fromRGB(220, 240, 255)
resetKorbloxBtn.Text = "Reset Korblox"
resetKorbloxBtn.AutoButtonColor = true
resetKorbloxBtn.ZIndex = 2
resetKorbloxBtn.Parent = mainTab
local UICorner_korbloxReset = Instance.new("UICorner")
UICorner_korbloxReset.CornerRadius = UDim.new(0, 8)
UICorner_korbloxReset.Parent = resetKorbloxBtn

local origKorbloxMesh, origKorbloxTex
korbloxBtn.MouseButton1Click:Connect(function()
    local char = LocalPlayer.Character
    if char then
        local rleg = char:FindFirstChild("RightLowerLeg") or char:FindFirstChild("Right Leg")
        local rfoot = char:FindFirstChild("RightFoot")
        if rleg and rleg:IsA("MeshPart") then
            if not origKorbloxMesh then origKorbloxMesh = rleg.MeshId end
            if not origKorbloxTex then origKorbloxTex = rleg.TextureID end
            rleg.MeshId = "902942093"
            rleg.TextureID = "902843398"
        end
        if rfoot and rfoot:IsA("MeshPart") then
            rfoot.MeshId = "902942093"
            rfoot.TextureID = "902843398"
        end
    end
end)
resetKorbloxBtn.MouseButton1Click:Connect(function()
    local char = LocalPlayer.Character
    if char then
        local rleg = char:FindFirstChild("RightLowerLeg") or char:FindFirstChild("Right Leg")
        local rfoot = char:FindFirstChild("RightFoot")
        if rleg and rleg:IsA("MeshPart") and origKorbloxMesh and origKorbloxTex then
            rleg.MeshId = origKorbloxMesh
            rleg.TextureID = origKorbloxTex
        end
        if rfoot and rfoot:IsA("MeshPart") and origKorbloxMesh and origKorbloxTex then
            rfoot.MeshId = origKorbloxMesh
            rfoot.TextureID = origKorbloxTex
        end
    end
end)

-- ALCE
local mooseBtn = Instance.new("TextButton")
mooseBtn.Size = UDim2.new(0, 180, 0, 36)
mooseBtn.Position = UDim2.new(0, 10, 0, 380)
mooseBtn.BackgroundColor3 = Color3.fromRGB(80, 60, 30)
mooseBtn.Font = Enum.Font.GothamBold
mooseBtn.TextSize = 17
mooseBtn.TextColor3 = Color3.fromRGB(255,255,230)
mooseBtn.Text = "Ativar Alce"
mooseBtn.AutoButtonColor = true
mooseBtn.ZIndex = 2
mooseBtn.Parent = mainTab
local UICorner3 = Instance.new("UICorner")
UICorner3.CornerRadius = UDim.new(0, 10)
UICorner3.Parent = mooseBtn
local resetMooseBtn = Instance.new("TextButton")
resetMooseBtn.Size = UDim2.new(0, 180, 0, 24)
resetMooseBtn.Position = UDim2.new(0, 10, 0, 420)
resetMooseBtn.BackgroundColor3 = Color3.fromRGB(110, 90, 60)
resetMooseBtn.Font = Enum.Font.Gotham
resetMooseBtn.TextSize = 15
resetMooseBtn.TextColor3 = Color3.fromRGB(255,255,230)
resetMooseBtn.Text = "Reset Alce"
resetMooseBtn.AutoButtonColor = true
resetMooseBtn.ZIndex = 2
resetMooseBtn.Parent = mainTab
local UICorner3b = Instance.new("UICorner")
UICorner3b.CornerRadius = UDim.new(0, 10)
UICorner3b.Parent = resetMooseBtn

mooseBtn.MouseButton1Click:Connect(function()
    local char = LocalPlayer.Character
    if char then
        if char:FindFirstChild("A Vingança do Alce") then return end
        local acc = Instance.new("Accessory")
        acc.Name = "A Vingança do Alce"
        local handle = Instance.new("Part")
        handle.Name = "Handle"
        handle.Size = Vector3.new(1,1,1)
        handle.Parent = acc
        local mesh = Instance.new("SpecialMesh")
        mesh.MeshType = Enum.MeshType.FileMesh
        mesh.MeshId = "rbxassetid://55318094"
        mesh.TextureId = "rbxassetid://55318060"
        mesh.Parent = handle
        handle.Massless = true
        handle.CanCollide = false
        handle.Transparency = 0
        acc.Parent = char
        pcall(function() acc.AttachmentPoint = CFrame.new(0, 0.3, 0) end)
    end
end)
resetMooseBtn.MouseButton1Click:Connect(function()
    local char = LocalPlayer.Character
    if char then
        if char:FindFirstChild("A Vingança do Alce") then
            char["A Vingança do Alce"]:Destroy()
        end
    end
end)

---------------- ESP TAB ----------------
local EspTab = tabContents[2]

local toggleESPBtn = Instance.new("TextButton")
toggleESPBtn.Size = UDim2.new(0, 180, 0, 38)
toggleESPBtn.Position = UDim2.new(0, 8, 0, 10)
toggleESPBtn.BackgroundColor3 = Color3.fromRGB(70, 30, 90)
toggleESPBtn.Font = Enum.Font.GothamBold
toggleESPBtn.TextSize = 20
toggleESPBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
toggleESPBtn.AutoButtonColor = true
toggleESPBtn.ZIndex = 2
toggleESPBtn.Parent = EspTab
local UICorner_esp = Instance.new("UICorner")
UICorner_esp.CornerRadius = UDim.new(0, 8)
UICorner_esp.Parent = toggleESPBtn

local function updateToggleESPBtn()
    toggleESPBtn.Text = ESP_ENABLED and "Desativar ESP" or "Ativar ESP"
    toggleESPBtn.BackgroundColor3 = ESP_ENABLED and Color3.fromRGB(100, 40, 120) or Color3.fromRGB(70, 30, 90)
end
toggleESPBtn.MouseButton1Click:Connect(function()
    setESPState(not ESP_ENABLED)
    updateToggleESPBtn()
end)
updateToggleESPBtn()

local colorLabel = Instance.new("TextLabel")
colorLabel.Size = UDim2.new(0, 140, 0, 26)
colorLabel.Position = UDim2.new(0, 8, 0, 62)
colorLabel.BackgroundTransparency = 1
colorLabel.Font = Enum.Font.Gotham
colorLabel.Text = "Cor do Highlight:"
colorLabel.TextSize = 18
colorLabel.TextColor3 = Color3.fromRGB(240, 230, 255)
colorLabel.TextXAlignment = Enum.TextXAlignment.Left
colorLabel.ZIndex = 2
colorLabel.Parent = EspTab

local colorDropdown = Instance.new("TextButton")
colorDropdown.Size = UDim2.new(0, 90, 0, 26)
colorDropdown.Position = UDim2.new(0, 150, 0, 62)
colorDropdown.BackgroundColor3 = Color3.fromRGB(40, 20, 60)
colorDropdown.Font = Enum.Font.GothamSemibold
colorDropdown.TextSize = 18
colorDropdown.TextColor3 = Color3.fromRGB(230, 210, 255)
colorDropdown.Text = highlightColor
colorDropdown.ZIndex = 3
colorDropdown.Parent = EspTab
local UICorner_colorDropdown = Instance.new("UICorner")
UICorner_colorDropdown.CornerRadius = UDim.new(0, 6)
UICorner_colorDropdown.Parent = colorDropdown

local dropOpen = false
local colorOptionsFrame = Instance.new("Frame")
colorOptionsFrame.Size = UDim2.new(1, 0, 0, 106)
colorOptionsFrame.Position = UDim2.new(0, 0, 0, 26)
colorOptionsFrame.BackgroundColor3 = Color3.fromRGB(30, 15, 45)
colorOptionsFrame.BorderSizePixel = 0
colorOptionsFrame.Visible = false
colorOptionsFrame.ZIndex = 4
colorOptionsFrame.Parent = colorDropdown
local UICorner_colorFrame = Instance.new("UICorner")
UICorner_colorFrame.CornerRadius = UDim.new(0,6)
UICorner_colorFrame.Parent = colorOptionsFrame

for idx, cname in ipairs({"Roxo", "Amarelo", "Verde", "Azul"}) do
    local opt = Instance.new("TextButton")
    opt.Size = UDim2.new(1,0,0,24)
    opt.Position = UDim2.new(0,0,0,(idx-1)*26)
    opt.BackgroundColor3 = Color3.fromRGB(40, 20, 60)
    opt.Text = cname
    opt.Font = Enum.Font.Gotham
    opt.TextSize = 18
    opt.TextColor3 = Color3.fromRGB(230,230,255)
    opt.ZIndex = 5
    opt.Parent = colorOptionsFrame
    local cornerOpt = Instance.new("UICorner")
    cornerOpt.CornerRadius = UDim.new(0,4)
    cornerOpt.Parent = opt
    opt.MouseButton1Click:Connect(function()
        highlightColor = cname
        colorDropdown.Text = cname
        colorOptionsFrame.Visible = false
        dropOpen = false
        updateHighlightColors()
    end)
end
colorDropdown.MouseButton1Click:Connect(function()
    dropOpen = not dropOpen
    colorOptionsFrame.Visible = dropOpen
    colorOptionsFrame.ZIndex = 4
    colorDropdown.ZIndex = dropOpen and 5 or 3
end)

local keybindLabel = Instance.new("TextLabel")
keybindLabel.Size = UDim2.new(0, 160, 0, 28)
keybindLabel.Position = UDim2.new(0, 8, 0, 102)
keybindLabel.BackgroundTransparency = 1
keybindLabel.Font = Enum.Font.Gotham
keybindLabel.Text = "Tecla para ESP:"
keybindLabel.TextSize = 18
keybindLabel.TextColor3 = Color3.fromRGB(230, 210, 255)
keybindLabel.ZIndex = 2
keybindLabel.Parent = EspTab

local keybindBtn = Instance.new("TextButton")
keybindBtn.Size = UDim2.new(0, 70, 0, 28)
keybindBtn.Position = UDim2.new(0, 172, 0, 102)
keybindBtn.BackgroundColor3 = Color3.fromRGB(40, 20, 60)
keybindBtn.Font = Enum.Font.GothamSemibold
keybindBtn.TextSize = 18
keybindBtn.TextColor3 = Color3.fromRGB(230, 210, 255)
keybindBtn.Text = toggleKey.Name
keybindBtn.ZIndex = 2
keybindBtn.Parent = EspTab
local UICorner_keybindBtn = Instance.new("UICorner")
UICorner_keybindBtn.CornerRadius = UDim.new(0, 6)
UICorner_keybindBtn.Parent = keybindBtn

keybindBtn.MouseButton1Click:Connect(function()
    if waitingForKey then return end
    keybindBtn.Text = "Pressione..."
    waitingForKey = true
end)

-- CHAMS BUTTON
local chamsBtn = Instance.new("TextButton")
chamsBtn.Size = UDim2.new(0, 180, 0, 38)
chamsBtn.Position = UDim2.new(0, 8, 0, 144)
chamsBtn.BackgroundColor3 = Color3.fromRGB(20, 120, 165)
chamsBtn.Font = Enum.Font.GothamBold
chamsBtn.TextSize = 20
chamsBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
chamsBtn.AutoButtonColor = true
chamsBtn.Text = "Ativar Chams"
chamsBtn.ZIndex = 2
chamsBtn.Parent = EspTab
local UICorner_chams = Instance.new("UICorner")
UICorner_chams.CornerRadius = UDim.new(0, 8)
UICorner_chams.Parent = chamsBtn

local function updateChamsBtn()
    chamsBtn.Text = CHAMS_ENABLED and "Desativar Chams" or "Ativar Chams"
    chamsBtn.BackgroundColor3 = CHAMS_ENABLED and Color3.fromRGB(0, 190, 220) or Color3.fromRGB(20, 120, 165)
end
chamsBtn.MouseButton1Click:Connect(function()
    setChams(not CHAMS_ENABLED)
    updateChamsBtn()
end)
updateChamsBtn()

---------------- INFO TAB ----------------
local infoTab = tabContents[3]
local infoLabel = Instance.new("TextLabel")
infoLabel.Size = UDim2.new(1, 0, 1, 0)
infoLabel.Position = UDim2.new(0, 0, 0, 0)
infoLabel.BackgroundTransparency = 1
infoLabel.Font = Enum.Font.Gotham
infoLabel.TextWrapped = true
infoLabel.Text = "Menu ESP feito por Notzin.\n\nAba Esp: ESP, Chams, cor do ESP, tecla toggle.\nAba Main: Speed/JumpPower, Invisível, Fly, Skin Changer (Headless, Korblox, Alce).\nMenu arredondado, abas laterais, abre/fecha com P."
infoLabel.TextSize = 18
infoLabel.TextColor3 = Color3.fromRGB(220, 220, 240)
infoLabel.ZIndex = 2
infoLabel.Parent = infoTab

-- Inicializa exibindo Main tab
showTab(1)
MainFrame.Visible = true

-- ========== ATALHOS GLOBAIS ==========
UserInputService.InputBegan:Connect(function(input, processed)
    if not processed then
        -- ESP keybind
        if waitingForKey and input.UserInputType == Enum.UserInputType.Keyboard then
            toggleKey = input.KeyCode
            keybindBtn.Text = toggleKey.Name
            waitingForKey = false
        elseif input.KeyCode == toggleKey and not waitingForKey then
            setESPState(not ESP_ENABLED)
            updateToggleESPBtn()
        -- Invisível keybind
        elseif waitingInvisKey and input.UserInputType == Enum.UserInputType.Keyboard then
            INVIS_KEY = input.KeyCode
            invisKeyBtn.Text = INVIS_KEY.Name
            waitingInvisKey = false
        elseif input.KeyCode == INVIS_KEY and not waitingInvisKey then
            setInvisible(not INVIS_ENABLED)
            updateInvisBtn()
        -- Fly keybind
        elseif waitingFlyKey and input.UserInputType == Enum.UserInputType.Keyboard then
            FLY_KEY = input.KeyCode
            flyKeyBtn.Text = FLY_KEY.Name
            waitingFlyKey = false
        elseif input.KeyCode == FLY_KEY and not waitingFlyKey then
            setFly(not FLY_ENABLED)
            updateFlyBtn()
        -- Abrir/fechar menu
        elseif input.KeyCode == Enum.KeyCode.P then
            MainFrame.Visible = not MainFrame.Visible
        end
    end
end)

local origDraggable = MainFrame.Draggable
MainFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        local mouse = UserInputService:GetMouseLocation()
        for _, obj in ipairs(mainTab:GetChildren()) do
            if obj:IsA("Frame") and obj.AbsoluteSize.Y == 12 then
                local abs = obj.AbsolutePosition
                local size = obj.AbsoluteSize
                if mouse.X >= abs.X and mouse.X <= abs.X+size.X and mouse.Y >= abs.Y and mouse.Y <= abs.Y+size.Y then
                    MainFrame.Draggable = false
                    return
                end
            end
        end
        MainFrame.Draggable = origDraggable
    end
end)
UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        MainFrame.Draggable = origDraggable
    end
end)
