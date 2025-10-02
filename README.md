-- Bito Scripts (TEST TOOLS) - Mobile-friendly + draggable UI
-- USO: Apenas para testes/Desenvolvimento.
-- Aim-lock (AIM) só disponível em Studio ou para UserIds na allowlist.

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local CoreGui = game:GetService("CoreGui")

-- === CONFIGURA AQUI ===
local AllowedUserIds = { 7735154588 } -- put your UserId(s) here
-- =======================

-- helper: permission check
local function hasPermission()
    if RunService:IsStudio() then return true end
    local ok, isCreator = pcall(function()
        return (type(game.CreatorId) == "number" and LocalPlayer.UserId == game.CreatorId)
    end)
    if ok and isCreator then return true end
    for _, id in ipairs(AllowedUserIds) do
        if LocalPlayer.UserId == id then return true end
    end
    return false
end

-- cleanup old
if CoreGui:FindFirstChild("BitoScriptsGUI") then
    CoreGui.BitoScriptsGUI:Destroy()
end

-- === GUI ===
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "BitoScriptsGUI"
screenGui.Parent = CoreGui
screenGui.ResetOnSpawn = false
screenGui.IgnoreGuiInset = true

-- Open button (bottom-right)
local openButton = Instance.new("ImageButton")
openButton.Name = "OpenBitoButton"
openButton.Size = UDim2.new(0, 56, 0, 56)
openButton.AnchorPoint = Vector2.new(1, 1)
openButton.Position = UDim2.new(1, -18, 1, -18)
openButton.BackgroundColor3 = Color3.fromRGB(200, 30, 30)
openButton.Image = "rbxassetid://3926305904"
openButton.Parent = screenGui
local obCorner = Instance.new("UICorner", openButton); obCorner.CornerRadius = UDim.new(1,0)
local obStroke = Instance.new("UIStroke", openButton); obStroke.Thickness = 1; obStroke.Color = Color3.fromRGB(255,255,255)

-- Main window
local mainFrame = Instance.new("Frame")
mainFrame.Name = "BitoMain"
mainFrame.Size = UDim2.new(0, 380, 0, 360)
mainFrame.Position = UDim2.new(0.5, -190, 0.5, -180)
mainFrame.BackgroundColor3 = Color3.fromRGB(20,20,20)
mainFrame.BorderSizePixel = 0
mainFrame.Visible = false
mainFrame.Parent = screenGui
local mfCorner = Instance.new("UICorner", mainFrame); mfCorner.CornerRadius = UDim.new(0,10)
local mfStroke = Instance.new("UIStroke", mainFrame); mfStroke.Thickness = 2; mfStroke.Color = Color3.fromRGB(150,0,0)

-- Title bar (draggable area)
local titleBar = Instance.new("Frame", mainFrame)
titleBar.Size = UDim2.new(1, 0, 0, 36)
titleBar.Position = UDim2.new(0, 0, 0, 0)
titleBar.BackgroundTransparency = 1

local title = Instance.new("TextLabel", titleBar)
title.Size = UDim2.new(1, -40, 1, 0)
title.Position = UDim2.new(0, 10, 0, 0)
title.BackgroundTransparency = 1
title.Font = Enum.Font.GothamBold
title.TextSize = 18
title.TextColor3 = Color3.fromRGB(220,60,60)
title.TextXAlignment = Enum.TextXAlignment.Left
title.Text = "Bito Scripts — TEST TOOLS"

-- Close button
local closeButton = Instance.new("TextButton", titleBar)
closeButton.Size = UDim2.new(0, 26, 0, 26)
closeButton.Position = UDim2.new(1, -36, 0, 5)
closeButton.Text = "X"
closeButton.Font = Enum.Font.GothamBold
closeButton.TextSize = 14
closeButton.BackgroundColor3 = Color3.fromRGB(60,10,10)
closeButton.TextColor3 = Color3.fromRGB(255,255,255)
local closeCorner = Instance.new("UICorner", closeButton); closeCorner.CornerRadius = UDim.new(0,6)

-- Body controls
local espButton = Instance.new("TextButton", mainFrame)
espButton.Size = UDim2.new(0, 170, 0, 44)
espButton.Position = UDim2.new(0.5, -180, 0, 70)
espButton.BackgroundColor3 = Color3.fromRGB(60,0,0)
espButton.Font = Enum.Font.GothamBold
espButton.TextSize = 16
espButton.TextColor3 = Color3.fromRGB(255,255,255)
espButton.Text = "ESP: OFF"
local espCorner = Instance.new("UICorner", espButton); espCorner.CornerRadius = UDim.new(0,8)

local aimButton = Instance.new("TextButton", mainFrame)
aimButton.Size = UDim2.new(0, 170, 0, 44)
aimButton.Position = UDim2.new(0.5, 10, 0, 70)
aimButton.BackgroundColor3 = Color3.fromRGB(60,0,0)
aimButton.Font = Enum.Font.GothamBold
aimButton.TextSize = 16
aimButton.TextColor3 = Color3.fromRGB(255,255,255)
aimButton.Text = "AIM: OFF"
local aimCorner = Instance.new("UICorner", aimButton); aimCorner.CornerRadius = UDim.new(0,8)

local infoLabel = Instance.new("TextLabel", mainFrame)
infoLabel.Size = UDim2.new(1, -20, 0, 56)
infoLabel.Position = UDim2.new(0, 10, 0, 130)
infoLabel.BackgroundTransparency = 1
infoLabel.Font = Enum.Font.Gotham
infoLabel.TextSize = 13
infoLabel.TextColor3 = Color3.fromRGB(200,200,200)
infoLabel.TextWrapped = true
infoLabel.Text = "Uso: TESTE/DEV. Ajuste FOV e Suavidade. AIM só se permitido."

-- FOV label + slider
local fovLabel = Instance.new("TextLabel", mainFrame)
fovLabel.Size = UDim2.new(0.5, -12, 0, 20)
fovLabel.Position = UDim2.new(0, 10, 0, 190)
fovLabel.BackgroundTransparency = 1
fovLabel.Font = Enum.Font.Gotham
fovLabel.TextSize = 14
fovLabel.TextColor3 = Color3.fromRGB(255,255,255)
fovLabel.Text = "FOV: 150"

local fovSlider = Instance.new("Frame", mainFrame)
fovSlider.Size = UDim2.new(0.9, 0, 0, 18)
fovSlider.Position = UDim2.new(0.05, 0, 0, 220)
fovSlider.BackgroundColor3 = Color3.fromRGB(60, 10, 10)
local fovFill = Instance.new("Frame", fovSlider)
fovFill.Size = UDim2.new(0.375, 0, 1, 0)
fovFill.BackgroundColor3 = Color3.fromRGB(200, 40, 40)

-- Smooth label + slider
local smoothLabel = Instance.new("TextLabel", mainFrame)
smoothLabel.Size = UDim2.new(0.5, -12, 0, 20)
smoothLabel.Position = UDim2.new(0, 10, 0, 250)
smoothLabel.BackgroundTransparency = 1
smoothLabel.Font = Enum.Font.Gotham
smoothLabel.TextSize = 14
smoothLabel.TextColor3 = Color3.fromRGB(255,255,255)
smoothLabel.Text = "Suavidade: 0"

local smoothSlider = Instance.new("Frame", mainFrame)
smoothSlider.Size = UDim2.new(0.9, 0, 0, 18)
smoothSlider.Position = UDim2.new(0.05, 0, 0, 280)
smoothSlider.BackgroundColor3 = Color3.fromRGB(20, 60, 60)
local smoothFill = Instance.new("Frame", smoothSlider)
smoothFill.Size = UDim2.new(0, 0, 1, 0)
smoothFill.BackgroundColor3 = Color3.fromRGB(0, 200, 200)

local permLabel = Instance.new("TextLabel", mainFrame)
permLabel.Size = UDim2.new(1, -20, 0, 30)
permLabel.Position = UDim2.new(0, 10, 1, -40)
permLabel.BackgroundTransparency = 1
permLabel.Font = Enum.Font.Gotham
permLabel.TextSize = 12
permLabel.TextColor3 = Color3.fromRGB(200,200,200)

-- === STATES & DEFAULTS ===
local ESPEnabled = false
local AimEnabled = false
local FOV = 150
local Smoothness = 0
local aimAllowed = hasPermission()

if not aimAllowed then
    permLabel.Text = "AIM: BLOQUEADO — Studio/allowlist required."
    aimButton.Text = "AIM: BLOQ"
    aimButton.BackgroundColor3 = Color3.fromRGB(45,45,45)
    aimButton.AutoButtonColor = false
else
    permLabel.Text = "AIM: PERMITIDO"
end

local function updateFOVUI()
    local frac = math.clamp(FOV / 400, 0, 1)
    fovFill.Size = UDim2.new(frac, 0, 1, 0)
    fovLabel.Text = "FOV: " .. tostring(FOV)
end
local function updateSmoothUI()
    local frac = math.clamp(Smoothness / 100, 0, 1)
    smoothFill.Size = UDim2.new(frac, 0, 1, 0)
    smoothLabel.Text = "Suavidade: " .. tostring(Smoothness)
end
updateFOVUI()
updateSmoothUI()

-- OPEN / CLOSE
openButton.MouseButton1Click:Connect(function()
    mainFrame.Visible = not mainFrame.Visible
end)
closeButton.MouseButton1Click:Connect(function()
    mainFrame.Visible = false
end)

-- === Drag system (works with touch & mouse) ===
do
    local dragging = false
    local dragInput = nil
    local dragStart = nil
    local startPos = nil

    local function update(input)
        if not dragging or not dragStart or not startPos then return end
        local delta = input.Position - dragStart
        -- compute new position keeping frame inside screen
        local newX = startPos.X + delta.X
        local newY = startPos.Y + delta.Y
        -- convert pixel-based to scale (respecting viewport size)
        local viewport = workspace.CurrentCamera.ViewportSize
        local scaleX = math.clamp(newX / viewport.X, 0, 1) -- clamp to [0,1]
        local scaleY = math.clamp(newY / viewport.Y, 0, 1)
        mainFrame.Position = UDim2.new(scaleX, -mainFrame.Size.X.Offset/2, scaleY, -mainFrame.Size.Y.Offset/2)
    end

    titleBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragInput = input
            dragStart = input.Position
            -- startPos: get the top-left pixel position of the frame center
            local absPos = mainFrame.AbsolutePosition
            local viewport = workspace.CurrentCamera.ViewportSize
            startPos = Vector2.new(absPos.X + mainFrame.AbsoluteSize.X/2, absPos.Y + mainFrame.AbsoluteSize.Y/2)
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                    dragInput = nil
                    dragStart = nil
                    startPos = nil
                end
            end)
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput then
            update(input)
        end
    end)
end

-- === ESP functions ===
local function addHighlight(plr)
    if not plr.Character then return end
    if plr.Character:FindFirstChild("BitoESP") then return end
    if not plr.Character:FindFirstChild("HumanoidRootPart") then return end
    local h = Instance.new("Highlight")
    h.Name = "BitoESP"
    h.Parent = plr.Character
    h.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    h.FillTransparency = 0.5
    h.OutlineColor = Color3.fromRGB(255,255,255)
    if plr.Team == LocalPlayer.Team then
        h.FillColor = Color3.fromRGB(0,255,0)
    else
        h.FillColor = Color3.fromRGB(255,0,0)
    end
end

local function removeHighlight(plr)
    if plr.Character and plr.Character:FindFirstChild("BitoESP") then
        plr.Character.BitoESP:Destroy()
    end
end

local function enableESP()
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer then
            addHighlight(p)
            p.CharacterAdded:Connect(function() task.wait(0.5); if ESPEnabled then addHighlight(p) end end)
        end
    end
end
local function disableESP()
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LocalPlayer then
            removeHighlight(p)
        end
    end
end

espButton.MouseButton1Click:Connect(function()
    ESPEnabled = not ESPEnabled
    espButton.Text = ESPEnabled and "ESP: ON" or "ESP: OFF"
    espButton.BackgroundColor3 = ESPEnabled and Color3.fromRGB(0,120,0) or Color3.fromRGB(60,0,0)
    if ESPEnabled then enableESP() else disableESP() end
end)

-- AIM toggle (permission-checked)
aimButton.MouseButton1Click:Connect(function()
    if not aimAllowed then
        infoLabel.Text = "AIM bloqueado: somente Studio ou allowlist."
        return
    end
    AimEnabled = not AimEnabled
    aimButton.Text = AimEnabled and "AIM: ON" or "AIM: OFF"
    aimButton.BackgroundColor3 = AimEnabled and Color3.fromRGB(0,120,0) or Color3.fromRGB(60,0,0)
end)

-- slider interactivity (touch + mouse)
do
    local draggingF = false
    fovSlider.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
            draggingF = true
        end
    end)
    fovSlider.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
            draggingF = false
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if draggingF and (input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseMovement) then
            local absPos = fovSlider.AbsolutePosition
            local absSize = fovSlider.AbsoluteSize
            local x = (input.Position.X - absPos.X)
            local frac = math.clamp(x / absSize.X, 0, 1)
            FOV = math.floor(frac * 400)
            updateFOVUI()
        end
    end)
end

do
    local draggingS = false
    smoothSlider.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
            draggingS = true
        end
    end)
    smoothSlider.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
            draggingS = false
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if draggingS and (input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseMovement) then
            local absPos = smoothSlider.AbsolutePosition
            local absSize = smoothSlider.AbsoluteSize
            local x = (input.Position.X - absPos.X)
            local frac = math.clamp(x / absSize.X, 0, 1)
            Smoothness = math.floor(frac * 100)
            updateSmoothUI()
        end
    end)
end

-- keyboard fallback for FOV adjust (desktop)
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.Up then
        FOV = math.clamp(FOV + 10, 1, 400); updateFOVUI()
    elseif input.KeyCode == Enum.KeyCode.Down then
        FOV = math.clamp(FOV - 10, 1, 400); updateFOVUI()
    end
end)

-- AIM logic (mobile-friendly: center of screen)
RunService.RenderStepped:Connect(function()
    if AimEnabled and aimAllowed then
        local center = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
        local best, bestDist = nil, math.huge
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("Head") then
                local pos, onScreen = Camera:WorldToViewportPoint(p.Character.Head.Position)
                if onScreen then
                    local d = (Vector2.new(pos.X, pos.Y) - center).Magnitude
                    if d < bestDist and d <= FOV then
                        bestDist = d
                        best = p
                    end
                end
            end
        end

        if best and best.Character and best.Character:FindFirstChild("Head") then
            local target = best.Character.Head.Position
            local camPos = Camera.CFrame.Position
            local desired = CFrame.new(camPos, target)
            if Smoothness == 0 then
                Camera.CFrame = desired
            else
                local t = math.clamp(Smoothness/100, 0, 1)
                Camera.CFrame = Camera.CFrame:Lerp(desired, t)
            end
        end
    end
end)

-- final UI note
if not aimAllowed then
    infoLabel.Text = infoLabel.Text .. "\n[AIM BLOQUEADO: Studio ou allowlist necessária]"
else
    infoLabel.Text = infoLabel.Text .. "\n[AIM habilitado por permissão]"
end
