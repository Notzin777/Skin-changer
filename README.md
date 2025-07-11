--[[
SkinChangerMenu.lua V3
- Botões Headless, Reset Headless, Korblox, Reset Korblox
- Botão para equipar "A Vingança do Alce" (visual local)
- Botão Reset Alce
- Feito por notzin, abrir/fechar no P
--]]

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

-- GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "SkinChangerMenu"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 170, 0, 252)
Frame.Position = UDim2.new(0, 40, 0, 150)
Frame.BackgroundColor3 = Color3.fromRGB(30, 30, 45)
Frame.Active = true
Frame.Draggable = true
Frame.Visible = true
Frame.Parent = ScreenGui
local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 16)
UICorner.Parent = Frame

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 32)
Title.Position = UDim2.new(0, 0, 0, 0)
Title.BackgroundTransparency = 1
Title.Font = Enum.Font.GothamBold
Title.Text = "Skin Changer"
Title.TextSize = 20
Title.TextColor3 = Color3.fromRGB(170, 85, 255)
Title.Parent = Frame

-- Botão Headless
local headlessBtn = Instance.new("TextButton")
headlessBtn.Size = UDim2.new(1, -24, 0, 36)
headlessBtn.Position = UDim2.new(0, 12, 0, 38)
headlessBtn.BackgroundColor3 = Color3.fromRGB(55, 40, 100)
headlessBtn.Font = Enum.Font.GothamBold
headlessBtn.TextSize = 18
headlessBtn.TextColor3 = Color3.fromRGB(255,255,255)
headlessBtn.Text = "Ativar Headless"
headlessBtn.AutoButtonColor = true
headlessBtn.Parent = Frame
local UICorner1 = Instance.new("UICorner")
UICorner1.CornerRadius = UDim.new(0, 10)
UICorner1.Parent = headlessBtn

-- Botão RESET Headless
local resetHeadlessBtn = Instance.new("TextButton")
resetHeadlessBtn.Size = UDim2.new(1, -24, 0, 28)
resetHeadlessBtn.Position = UDim2.new(0, 12, 0, 80)
resetHeadlessBtn.BackgroundColor3 = Color3.fromRGB(80, 30, 50)
resetHeadlessBtn.Font = Enum.Font.Gotham
resetHeadlessBtn.TextSize = 16
resetHeadlessBtn.TextColor3 = Color3.fromRGB(255, 220, 220)
resetHeadlessBtn.Text = "Reset Headless"
resetHeadlessBtn.AutoButtonColor = true
resetHeadlessBtn.Parent = Frame
local UICorner1b = Instance.new("UICorner")
UICorner1b.CornerRadius = UDim.new(0, 10)
UICorner1b.Parent = resetHeadlessBtn

-- Botão Korblox
local korbloxBtn = Instance.new("TextButton")
korbloxBtn.Size = UDim2.new(1, -24, 0, 36)
korbloxBtn.Position = UDim2.new(0, 12, 0, 114)
korbloxBtn.BackgroundColor3 = Color3.fromRGB(55, 80, 120)
korbloxBtn.Font = Enum.Font.GothamBold
korbloxBtn.TextSize = 18
korbloxBtn.TextColor3 = Color3.fromRGB(255,255,255)
korbloxBtn.Text = "Ativar Korblox"
korbloxBtn.AutoButtonColor = true
korbloxBtn.Parent = Frame
local UICorner2 = Instance.new("UICorner")
UICorner2.CornerRadius = UDim.new(0, 10)
UICorner2.Parent = korbloxBtn

-- Botão RESET Korblox
local resetKorbloxBtn = Instance.new("TextButton")
resetKorbloxBtn.Size = UDim2.new(1, -24, 0, 28)
resetKorbloxBtn.Position = UDim2.new(0, 12, 0, 156)
resetKorbloxBtn.BackgroundColor3 = Color3.fromRGB(80, 55, 100)
resetKorbloxBtn.Font = Enum.Font.Gotham
resetKorbloxBtn.TextSize = 16
resetKorbloxBtn.TextColor3 = Color3.fromRGB(220, 240, 255)
resetKorbloxBtn.Text = "Reset Korblox"
resetKorbloxBtn.AutoButtonColor = true
resetKorbloxBtn.Parent = Frame
local UICorner2b = Instance.new("UICorner")
UICorner2b.CornerRadius = UDim.new(0, 10)
UICorner2b.Parent = resetKorbloxBtn

-- Botão Vingança do Alce
local mooseBtn = Instance.new("TextButton")
mooseBtn.Size = UDim2.new(1, -24, 0, 36)
mooseBtn.Position = UDim2.new(0, 12, 0, 190)
mooseBtn.BackgroundColor3 = Color3.fromRGB(80, 60, 30)
mooseBtn.Font = Enum.Font.GothamBold
mooseBtn.TextSize = 17
mooseBtn.TextColor3 = Color3.fromRGB(255,255,230)
mooseBtn.Text = "Ativar Alce"
mooseBtn.AutoButtonColor = true
mooseBtn.Parent = Frame
local UICorner3 = Instance.new("UICorner")
UICorner3.CornerRadius = UDim.new(0, 10)
UICorner3.Parent = mooseBtn

-- Botão RESET Alce
local resetMooseBtn = Instance.new("TextButton")
resetMooseBtn.Size = UDim2.new(1, -24, 0, 24)
resetMooseBtn.Position = UDim2.new(0, 12, 0, 230)
resetMooseBtn.BackgroundColor3 = Color3.fromRGB(110, 90, 60)
resetMooseBtn.Font = Enum.Font.Gotham
resetMooseBtn.TextSize = 15
resetMooseBtn.TextColor3 = Color3.fromRGB(255,255,230)
resetMooseBtn.Text = "Reset Alce"
resetMooseBtn.AutoButtonColor = true
resetMooseBtn.Parent = Frame
local UICorner3b = Instance.new("UICorner")
UICorner3b.CornerRadius = UDim.new(0, 10)
UICorner3b.Parent = resetMooseBtn

-- Crédito
local credit = Instance.new("TextLabel")
credit.Size = UDim2.new(1, 0, 0, 18)
credit.Position = UDim2.new(0, 0, 1, -18)
credit.BackgroundTransparency = 1
credit.Font = Enum.Font.Gotham
credit.Text = "feito por notzin"
credit.TextSize = 15
credit.TextColor3 = Color3.fromRGB(185, 140, 255)
credit.TextYAlignment = Enum.TextYAlignment.Center
credit.Parent = Frame

-- Função Headless
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

-- Função Korblox
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

-- Função Vingança do Alce (Accessory ID: 11684330)
local ALCE_ASSETID = 11684330
local alceAcc
mooseBtn.MouseButton1Click:Connect(function()
    local char = LocalPlayer.Character
    if char then
        -- Checa se já existe
        if char:FindFirstChild("A Vingança do Alce") then return end
        local acc = Instance.new("Accessory")
        acc.Name = "A Vingança do Alce"
        local handle = Instance.new("Part")
        handle.Name = "Handle"
        handle.Size = Vector3.new(1,1,1)
        handle.Parent = acc
        -- SpecialMesh do item
        local mesh = Instance.new("SpecialMesh")
        mesh.MeshType = Enum.MeshType.FileMesh
        mesh.MeshId = "rbxassetid://55318094"
        mesh.TextureId = "rbxassetid://55318060"
        mesh.Parent = handle
        -- Ajusta posição
        handle.Massless = true
        handle.CanCollide = false
        handle.Transparency = 0
        acc.Parent = char
        alceAcc = acc
        -- Posiciona como Hat
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

-- Abrir/fechar no P
UserInputService.InputBegan:Connect(function(input, gp)
    if not gp and input.KeyCode == Enum.KeyCode.P then
        Frame.Visible = not Frame.Visible
    end
end)
