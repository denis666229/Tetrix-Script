-- Удаляем старое меню, если оно уже существует
if game.CoreGui:FindFirstChild("MyCustomMenu") then
    game.CoreGui:FindFirstChild("MyCustomMenu"):Destroy()
end

-- Создание GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "MyCustomMenu"
ScreenGui.Parent = game.CoreGui
ScreenGui.Enabled = false -- Изначально меню скрыто

local Frame = Instance.new("Frame")
Frame.Parent = ScreenGui
Frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Frame.Position = UDim2.new(0.3, 0, 0.2, 0)
Frame.Size = UDim2.new(0, 520, 0, 520)
Frame.Active = true
Frame.Draggable = true

-- Заголовок (Tetrix Script)
local TitleLabel = Instance.new("TextLabel")
TitleLabel.Parent = Frame
TitleLabel.Size = UDim2.new(0, 220, 0, 30)
TitleLabel.Position = UDim2.new(0, 15, 0, 5)
TitleLabel.BackgroundTransparency = 1
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.Font = Enum.Font.SourceSansBold
TitleLabel.TextSize = 22
TitleLabel.Text = "Tetrix Script"
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left

-- Кнопка закрытия (X)
local CloseButton = Instance.new("TextButton")
CloseButton.Parent = Frame
CloseButton.Size = UDim2.new(0, 30, 0, 30)
CloseButton.Position = UDim2.new(1, -40, 0, 5) 
CloseButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseButton.Font = Enum.Font.SourceSansBold
CloseButton.TextSize = 20

CloseButton.MouseButton1Click:Connect(function()
    ScreenGui.Enabled = false -- Просто скрываем меню
end)

-- Функция для показа стандартного уведомления в Roblox
local function showNotification(title, text, duration)
    local success, err = pcall(function()
        game.StarterGui:SetCore("SendNotification", {
            Title = title;
            Text = text;
            Duration = duration;
        })
    end)

    if not success then
        warn("Ошибка показа уведомления:", err)
    end
end

-- Флаг для первого открытия
local firstTimeOpen = true

-- Переключение меню по Left Alt
local UserInputService = game:GetService("UserInputService")

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.KeyCode == Enum.KeyCode.LeftAlt then
        ScreenGui.Enabled = not ScreenGui.Enabled -- Переключаем видимость меню

        if ScreenGui.Enabled and firstTimeOpen then
            firstTimeOpen = false
            showNotification("Tetrix Script", "Press Left Alt to toggle menu", 3)
        end
    end
end)

-- Первое уведомление сразу после запуска скрипта
showNotification("Tetrix Script Loaded!", "Press Left Alt to open the menu", 5)



-- Left panel for tabs
local TabsFrame = Instance.new("Frame")
TabsFrame.Parent = Frame
TabsFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
TabsFrame.Size = UDim2.new(0, 160, 0, 480) -- Adjusted for symmetrical alignment
TabsFrame.Position = UDim2.new(0, 0, 0, 40)

-- Active tab indicator (rounded blue bar)
local ActiveIndicator = Instance.new("Frame")
ActiveIndicator.Parent = TabsFrame
ActiveIndicator.BackgroundColor3 = Color3.fromRGB(0, 122, 255) -- Blue color
ActiveIndicator.Size = UDim2.new(0, 8, 0, 50)
ActiveIndicator.Position = UDim2.new(0, 1, 0, 0) -- Aligned directly with tabs
ActiveIndicator.Visible = false

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 5) -- Rounded edges
UICorner.Parent = ActiveIndicator

-- Main buttons container
local ButtonsFrame = Instance.new("Frame")
ButtonsFrame.Parent = Frame
ButtonsFrame.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
ButtonsFrame.Size = UDim2.new(0, 350, 0, 480)
ButtonsFrame.Position = UDim2.new(0, 165, 0, 40)

-- Function to create a tab
local function createTab(name, text, position, callback)
    local tab = Instance.new("TextButton")
    tab.Parent = TabsFrame
    tab.Size = UDim2.new(0, 140, 0, 50) -- Adjusted width for symmetry
    tab.Position = UDim2.new(0, 10, 0, position)
    tab.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
    tab.Text = text
    tab.TextColor3 = Color3.fromRGB(255, 255, 255)
    tab.Font = Enum.Font.SourceSansBold
    tab.TextSize = 18

    tab.MouseButton1Click:Connect(function()
        ActiveIndicator.Visible = true
        ActiveIndicator.Position = UDim2.new(0, 1, 0, position) -- Align indicator directly with tab
        callback()
    end)
    return tab
end

-- Function to create a button
local function createButton(name, text, position)
    local button = Instance.new("TextButton")
    button.Parent = ButtonsFrame
    button.Size = UDim2.new(0, 330, 0, 50)
    button.Position = UDim2.new(0, 10, 0, position)
    button.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    button.Text = text
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.Font = Enum.Font.SourceSansBold
    button.TextSize = 18
    return button
end

-- Function to clear buttons when switching tabs
local function clearButtons()
    for _, v in pairs(ButtonsFrame:GetChildren()) do 
        if v:IsA("TextButton") or v:IsA("TextLabel") then v:Destroy() end 
    end
end


-- Tab: "Main"
local MainTab = createTab("MainTab", "Main", 10, function()
    clearButtons()
    local TPButton = createButton("TPButton", "Teleport to Spawn", 20)
    TPButton.MouseButton1Click:Connect(function()
        local player = game.Players.LocalPlayer
        if player and player.Character then
            player.Character:MoveTo(Vector3.new(0, 50, 0))
        end
    end)

    local SpeedButton = createButton("SpeedButton", "Speed x2", 80)
    SpeedButton.MouseButton1Click:Connect(function()
        local player = game.Players.LocalPlayer
        if player and player.Character and player.Character:FindFirstChild("Humanoid") then
            player.Character.Humanoid.WalkSpeed = 32
        end
    end)
end)

-- Tab: "Player"
local PlayerTab = createTab("PlayerTab", "Player", 70, function()
    clearButtons()
    local GodModeButton = createButton("GodModeButton", "God Mode", 20)
    GodModeButton.MouseButton1Click:Connect(function()
        local player = game.Players.LocalPlayer
        if player and player.Character and player.Character:FindFirstChild("Humanoid") then
            player.Character.Humanoid.MaxHealth = math.huge
            player.Character.Humanoid.Health = math.huge
        end
    end)

    local JumpButton = createButton("JumpButton", "Infinite Jump", 80)
    JumpButton.MouseButton1Click:Connect(function()
        game.Players.LocalPlayer.Character.Humanoid.UseJumpPower = true
        game.Players.LocalPlayer.Character.Humanoid.JumpPower = 150
    end)
end)

-- Tab: "Weapons"
local WeaponTab = createTab("WeaponTab", "Weapons", 130, function()
    clearButtons()
    local GiveGunButton = createButton("GiveGunButton", "Give Weapon", 20)
    GiveGunButton.MouseButton1Click:Connect(function()
        local player = game.Players.LocalPlayer
        local tool = Instance.new("Tool")
        tool.RequiresHandle = false
        tool.Name = "SuperGun"
        tool.Parent = player.Backpack
    end)
end)

-- Tab: "Teleport"
local TPMenu = createTab("TPMenu", "Teleport", 190, function()
    clearButtons()
    local TP1 = createButton("TP1", "Teleport to Point 1", 20)
    TP1.MouseButton1Click:Connect(function()
        game.Players.LocalPlayer.Character:MoveTo(Vector3.new(100, 10, 100))
    end)

    local TP2 = createButton("TP2", "Teleport to Point 2", 80)
    TP2.MouseButton1Click:Connect(function()
        game.Players.LocalPlayer.Character:MoveTo(Vector3.new(-100, 10, -100))
    end)
end)

-- Tab: "Misc"
local MiscTab = createTab("MiscTab", "Misc", 250, function()
    clearButtons()
    local FlyButton = createButton("FlyButton", "Enable Fly", 20)
    FlyButton.MouseButton1Click:Connect(function()
        game.Players.LocalPlayer.Character.Humanoid.JumpPower = 200
    end)
end)

-- Tab: "Info"
local InfoTab = createTab("InfoTab", "Info", 310, function()
    clearButtons()
    
    -- Title: Tetrix Script
    local InfoTitle = Instance.new("TextLabel")
    InfoTitle.Parent = ButtonsFrame
    InfoTitle.Size = UDim2.new(0, 300, 0, 40)
    InfoTitle.Position = UDim2.new(0, 10, 0, 10)
    InfoTitle.BackgroundTransparency = 1
    InfoTitle.TextColor3 = Color3.fromRGB(0, 255, 0) -- Green color
    InfoTitle.Font = Enum.Font.SourceSansBold
    InfoTitle.TextSize = 24
    InfoTitle.Text = "Tetrix Script"
    InfoTitle.TextWrapped = true

    -- Info text
    local InfoLabel = Instance.new("TextLabel")
    InfoLabel.Parent = ButtonsFrame
    InfoLabel.Size = UDim2.new(0, 300, 0, 200)
    InfoLabel.Position = UDim2.new(0, 10, 0, 50)
    InfoLabel.BackgroundTransparency = 1
    InfoLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    InfoLabel.Font = Enum.Font.SourceSansBold
    InfoLabel.TextSize = 18
    InfoLabel.Text = "Tetrix Script is a powerful Lua-based cheat menu for Roblox.\n\n" ..
                     "- Supports teleportation, speed boost, god mode, and more.\n" ..
                     "- Designed for educational purposes.\n" ..
                     "- Easy to use and optimized for all exploit injectors.\n\n" ..
                     "Use at your own risk!"
    InfoLabel.TextWrapped = true

    -- "By Izida" label at the bottom
    local ByLabel = Instance.new("TextLabel")
    ByLabel.Parent = ButtonsFrame
    ByLabel.Size = UDim2.new(0, 300, 0, 30)
    ByLabel.Position = UDim2.new(0, 10, 0, 380) -- Bottom position
    ByLabel.BackgroundTransparency = 1
    ByLabel.TextColor3 = Color3.fromRGB(255, 0, 0) -- Red color
    ByLabel.Font = Enum.Font.SourceSansBold
    ByLabel.TextSize = 20
    ByLabel.Text = "By Izida"
    ByLabel.TextWrapped = true
end)

-- Auto-open "Main" on start
MainTab.MouseButton1Click:Fire()
