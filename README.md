-- muhmethacks v2.0 GUI Edition - kendi orbit scriptim kanka 🚀

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
local Root = Char:WaitForChild("HumanoidRootPart")

local orbiting = false
local targetPlayer = nil
local orbitRadius = 12
local orbitSpeed = 1.5
local angle = 0

local function findPlayer(namePart)
    namePart = namePart:lower()
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and (p.Name:lower():find(namePart, 1, true) or p.DisplayName:lower():find(namePart, 1, true)) then
            return p
        end
    end
    return nil
end

local function startOrbit()
    if orbiting or not targetPlayer then return end
    
    local targetChar = targetPlayer.Character
    if not targetChar or not targetChar:FindFirstChild("HumanoidRootPart") then
        print("Hedef yüklenmedi aq")
        return
    end
    
    orbiting = true
    print("Orbit başladı → " .. targetPlayer.Name)
    
    spawn(function()
        while orbiting and targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") do
            local targetRoot = targetPlayer.Character.HumanoidRootPart
            angle = angle + (orbitSpeed * 0.016)
            
            local offset = Vector3.new(
                math.cos(angle) * orbitRadius,
                5,
                math.sin(angle) * orbitRadius
            )
            
            Root.CFrame = CFrame.new(targetRoot.Position + offset) * CFrame.Angles(0, -angle + math.pi/2, 0)
            
            local hum = Char:FindFirstChild("Humanoid")
            if hum then hum.PlatformStand = true end
            
            RunService.Heartbeat:Wait()
        end
        orbiting = false
        if Char:FindFirstChild("Humanoid") then Char.Humanoid.PlatformStand = false end
        print("Orbit bitti")
    end)
end

local function stopOrbit()
    orbiting = false
    if Char:FindFirstChild("Humanoid") then Char.Humanoid.PlatformStand = false end
    print("Orbit durduruldu kanka")
end

-- GUI Oluştur
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = game.CoreGui
ScreenGui.Name = "MuhmethacksGUI"

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 250, 0, 320)
Frame.Position = UDim2.new(0.5, -125, 0.5, -160)
Frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
Frame.BorderSizePixel = 0
Frame.Active = true
Frame.Draggable = true
Frame.Parent = ScreenGui

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 40)
Title.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Title.Text = "muhmethacks - Orbit"
Title.TextColor3 = Color3.fromRGB(0, 255, 100)
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 24
Title.Parent = Frame

-- İsim textbox
local NameBox = Instance.new("TextBox")
NameBox.Size = UDim2.new(0.9, 0, 0, 35)
NameBox.Position = UDim2.new(0.05, 0, 0.15, 0)
NameBox.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
NameBox.TextColor3 = Color3.fromRGB(255, 255, 255)
NameBox.PlaceholderText = "Oyuncu ismi veya kısaltma"
NameBox.Text = ""
NameBox.Font = Enum.Font.SourceSans
NameBox.TextSize = 18
NameBox.Parent = Frame

-- Mesafe slider/textbox
local RadiusLabel = Instance.new("TextLabel")
RadiusLabel.Size = UDim2.new(0.9, 0, 0, 20)
RadiusLabel.Position = UDim2.new(0.05, 0, 0.30, 0)
RadiusLabel.BackgroundTransparency = 1
RadiusLabel.Text = "Mesafe: 12"
RadiusLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
RadiusLabel.Font = Enum.Font.SourceSans
RadiusLabel.TextSize = 16
RadiusLabel.Parent = Frame

local RadiusBox = Instance.new("TextBox")
RadiusBox.Size = UDim2.new(0.9, 0, 0, 30)
RadiusBox.Position = UDim2.new(0.05, 0, 0.35, 0)
RadiusBox.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
RadiusBox.TextColor3 = Color3.fromRGB(255, 255, 255)
RadiusBox.Text = "12"
RadiusBox.Font = Enum.Font.SourceSans
RadiusBox.TextSize = 18
RadiusBox.Parent = Frame

-- Hız slider/textbox
local SpeedLabel = Instance.new("TextLabel")
SpeedLabel.Size = UDim2.new(0.9, 0, 0, 20)
SpeedLabel.Position = UDim2.new(0.05, 0, 0.50, 0)
SpeedLabel.BackgroundTransparency = 1
SpeedLabel.Text = "Hız: 1.5"
SpeedLabel.TextColor3 = Color3.fromRGB(200, 200, 200)
SpeedLabel.Font = Enum.Font.SourceSans
SpeedLabel.TextSize = 16
SpeedLabel.Parent = Frame

local SpeedBox = Instance.new("TextBox")
SpeedBox.Size = UDim2.new(0.9, 0, 0, 30)
SpeedBox.Position = UDim2.new(0.05, 0, 0.55, 0)
SpeedBox.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
SpeedBox.TextColor3 = Color3.fromRGB(255, 255, 255)
SpeedBox.Text = "1.5"
SpeedBox.Font = Enum.Font.SourceSans
SpeedBox.TextSize = 18
SpeedBox.Parent = Frame

-- Başlat butonu
local StartButton = Instance.new("TextButton")
StartButton.Size = UDim2.new(0.9, 0, 0, 50)
StartButton.Position = UDim2.new(0.05, 0, 0.70, 0)
StartButton.BackgroundColor3 = Color3.fromRGB(0, 170, 80)
StartButton.Text = "ORBIT BAŞLAT"
StartButton.TextColor3 = Color3.fromRGB(255, 255, 255)
StartButton.Font = Enum.Font.SourceSansBold
StartButton.TextSize = 22
StartButton.Parent = Frame

StartButton.MouseButton1Click:Connect(function()
    local isim = NameBox.Text
    if isim == "" then return end
    
    local hedef = findPlayer(isim)
    if not hedef then
        print("Oyuncu bulunamadı: " .. isim)
        return
    end
    
    targetPlayer = hedef
    orbitRadius = tonumber(RadiusBox.Text) or 12
    orbitSpeed = tonumber(SpeedBox.Text) or 1.5
    
    RadiusLabel.Text = "Mesafe: " .. orbitRadius
    SpeedLabel.Text = "Hız: " .. orbitSpeed
    
    startOrbit()
end)

-- Durdur butonu
local StopButton = Instance.new("TextButton")
StopButton.Size = UDim2.new(0.9, 0, 0, 50)
StopButton.Position = UDim2.new(0.05, 0, 0.85, 0)
StopButton.BackgroundColor3 = Color3.fromRGB(170, 0, 0)
StopButton.Text = "DURDUR"
StopButton.TextColor3 = Color3.fromRGB(255, 255, 255)
StopButton.Font = Enum.Font.SourceSansBold
StopButton.TextSize = 22
StopButton.Parent = Frame

StopButton.MouseButton1Click:Connect(stopOrbit)

-- Kapatma X
local CloseButton = Instance.new("TextButton")
CloseButton.Size = UDim2.new(0, 30, 0, 30)
CloseButton.Position = UDim2.new(1, -35, 0, 5)
CloseButton.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseButton.Font = Enum.Font.SourceSansBold
CloseButton.TextSize = 20
CloseButton.Parent = Frame

CloseButton.MouseButton1Click:Connect(function()
    ScreenGui:Destroy()
end)

print("muhmethacks GUI yüklendi! Ekranı sürükle, isim yaz, mesafe/hız ayarla ve başlat kanka 😈")
