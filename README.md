--// WindUI Universal Hub
--// Cleaned & Organized Version
--// Creator: toolsadmin

local Players = game:GetService("Players")
local Lighting = game:GetService("Lighting")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")

local LocalPlayer = Players.LocalPlayer

--==================================================
-- WINDUI
--==================================================

local WindUI = loadstring(game:HttpGet(
    "https://raw.githubusercontent.com/Footagesus/WindUI/main/dist/main.lua"
))()

local Window = WindUI:CreateWindow({
    Title = "scriptDEX1 Universal Hub",
    Icon = "terminal",
    Author = "tools admin spy",
    Folder = "IrwanHubWindUI",
    Size = UDim2.fromOffset(560, 400),
    Theme = "Dark",
    Transparent = true,
    Resizable = true,
})

--==================================================
-- TABS
--==================================================

local Tabs = {
    Player = Window:Tab({
        Title = "Player",
        Icon = "user",
    }),

    Visuals = Window:Tab({
        Title = "Visuals",
        Icon = "eye",
    }),

    Script = Window:Tab({
        Title = "Script",
        Icon = "globe",
    }),

    Tool = Window:Tab({
        Title = "Tool",
        Icon = "wrench",
    }),

hacker = Window:Tab({
        Title = "hacker",
        Icon = "zap",
    }),

    Settings = Window:Tab({
        Title = "Settings",
        Icon = "settings",
    }),
}

--==================================================
-- PLAYER
--==================================================

--1. Slider untuk mengatur Kecepatan Lari (WalkSpeed)
Tabs.Player:Slider({
    Title = "WalkSpeed",
    Description = "Atur kecepatan lari karaktermu sesuka hati",
    Value = {
        Min = 16,
        Max = 300, -- Bisa diatur super ngebut sampai 300
        Default = 16,
    },
    Callback = function(Value)
        CustomWalkSpeed = Value
        -- Langsung ubah kalau togglenya lagi nyala
        if SpeedJumpEnabled then
            local character = LocalPlayer.Character
            if character then
                local humanoid = character:FindFirstChildOfClass("Humanoid")
                if humanoid then
                    humanoid.WalkSpeed = CustomWalkSpeed
                end
            end
        end
    end,
})

-- 2. Slider untuk mengatur Ketinggian Lompat (JumpPower)
Tabs.Player:Slider({
    Title = "JumpPower",
    Description = "Atur ketinggian lompat karaktermu",
    Value = {
        Min = 50,
        Max = 500, -- Bisa diatur melompat tinggi sampai 500
        Default = 50,
    },
    Callback = function(Value)
        CustomJumpPower = Value
        -- Langsung ubah kalau togglenya lagi nyala
        if SpeedJumpEnabled then
            local character = LocalPlayer.Character
            if character then
                local humanoid = character:FindFirstChildOfClass("Humanoid")
                if humanoid then
                    humanoid.UseJumpPower = true
                    humanoid.JumpPower = CustomJumpPower
                end
            end
        end
    end,
})

-- 3. Toggle untuk Mengaktifkan / Menonaktifkan Speed & Jump Hack
Tabs.Player:Toggle({
    Title = "Enable Speed Jump",
    Description = "Aktifkan untuk menerapkan kecepatan dan lompat tinggi",
    Value = false,
    Callback = function(Value)
        SpeedJumpEnabled = Value
        local character = LocalPlayer.Character
        if character then
            local humanoid = character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                if Value then
                    humanoid.WalkSpeed = CustomWalkSpeed
                    humanoid.UseJumpPower = true
                    humanoid.JumpPower = CustomJumpPower
                end
            end
        end
    end,
})

-- 4. Loop pengaman (Biar kecepatan dan lompatan tidak ke-reset kalau karakter respawn/mati)
task.spawn(function()
    while true do
        task.wait(0.5)
        if SpeedJumpEnabled then
            local character = LocalPlayer.Character
            if character then
                local humanoid = character:FindFirstChildOfClass("Humanoid")
                if humanoid then
                    if humanoid.WalkSpeed ~= CustomWalkSpeed then
                        humanoid.WalkSpeed = CustomWalkSpeed
                    end
                    if humanoid.JumpPower ~= CustomJumpPower then
                        humanoid.UseJumpPower = true
                        humanoid.JumpPower = CustomJumpPower
                    end
                end
            end
        end
    end
end)

-- Infinite Jump
local InfiniteJumpConnection

Tabs.Player:Toggle({
    Title = "Infinite Jump",
    Description = "Memungkinkan karakter melompat kembali saat berada di udara",
    Value = false,

    Callback = function(Value)
        if InfiniteJumpConnection then
            InfiniteJumpConnection:Disconnect()
            InfiniteJumpConnection = nil
        end

        if Value then
            InfiniteJumpConnection = UIS.JumpRequest:Connect(function()
                local Character = LocalPlayer.Character
                local Humanoid = Character and Character:FindFirstChildOfClass("Humanoid")

                if Humanoid then
                    Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
                end
            end)
        end
    end,
})

-- Noclip
local NoclipConnection

Tabs.Player:Toggle({
    Title = "Noclip",
    Description = "Menonaktifkan collision karakter",
    Value = false,

    Callback = function(Value)
        if NoclipConnection then
            NoclipConnection:Disconnect()
            NoclipConnection = nil
        end

        if Value then
            NoclipConnection = RunService.Stepped:Connect(function()
                local Character = LocalPlayer.Character

                if Character then
                    for _, Part in ipairs(Character:GetDescendants()) do
                        if Part:IsA("BasePart") then
                            Part.CanCollide = false
                        end
                    end
                end
            end)
        end
    end,
})

Tabs.Player:Slider({
    Title = "Spin",
    Description = "Mengatur kecepatan putaran karakter",
    Value = {
        Min = 10,
        Max = 200,
        Default = 50,
    },
    Callback = function(Value)
        _G.SpinSpeed = Value
    end,
})

-- Toggle buat mengaktifkan/mematikan putaran
Tabs.Player:Toggle({
    Title = "Enable Spin ",
    Description = "Membuat karakter berputar-putar terus menerus",
    Value = false,
    Callback = function(Value)
        _G.Spinning = Value
        
        local RunService = game:GetService("RunService")
        local LocalPlayer = game:GetService("Players").LocalPlayer
        
        if _G.SpinConnection then
            _G.SpinConnection:Disconnect()
            _G.SpinConnection = nil
        end
        
        if Value then
            _G.SpinConnection = RunService.RenderStepped:Connect(function()
                local character = LocalPlayer.Character
                if character and character:FindFirstChild("HumanoidRootPart") then
                    local rootPart = character.HumanoidRootPart
                    -- Memutar CFrame rootpart setiap frame
                    rootPart.CFrame = rootPart.CFrame * CFrame.Angles(0, math.rad(_G.SpinSpeed), 0)
                end
            end)
        end
    end,
})

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Workspace = game:GetService("Workspace")

_G.LockedTargetName = "Pilih Player"
_G.WeaponFreezeEnabled = false
_G.IsFreezingTarget = false

local function getPlayerList()
    local list = {"Pilih Player"}
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer then
            table.insert(list, p.Name)
        end
    end
    return list
end

--==================================================
-- VISUALS
--==================================================

-- Fullbright
local FullbrightConnection

local OldBrightness
local OldClockTime
local OldFogEnd
local OldGlobalShadows
local OldOutdoorAmbient

Tabs.Visuals:Toggle({
    Title = "Fullbright",
    Description = "Menerangi lingkungan game",
    Value = false,

    Callback = function(Value)

        -- Matikan connection lama
        if FullbrightConnection then
            FullbrightConnection:Disconnect()
            FullbrightConnection = nil
        end

        if Value then
            -- Simpan setting asli SATU KALI
            OldBrightness = Lighting.Brightness
            OldClockTime = Lighting.ClockTime
            OldFogEnd = Lighting.FogEnd
            OldGlobalShadows = Lighting.GlobalShadows
            OldOutdoorAmbient = Lighting.OutdoorAmbient

            local function ApplyFullbright()
                Lighting.Brightness = 2
                Lighting.ClockTime = 14
                Lighting.FogEnd = 100000
                Lighting.GlobalShadows = false
                Lighting.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
            end

            ApplyFullbright()

            -- Pertahankan Fullbright selama toggle ON
            FullbrightConnection = Lighting.Changed:Connect(function()
                ApplyFullbright()
            end)

        else
            -- Kembalikan setting Lighting seperti sebelum Fullbright
            if OldBrightness ~= nil then
                Lighting.Brightness = OldBrightness
            end

            if OldClockTime ~= nil then
                Lighting.ClockTime = OldClockTime
            end

            if OldFogEnd ~= nil then
                Lighting.FogEnd = OldFogEnd
            end

            if OldGlobalShadows ~= nil then
                Lighting.GlobalShadows = OldGlobalShadows
            end

            if OldOutdoorAmbient ~= nil then
                Lighting.OutdoorAmbient = OldOutdoorAmbient
            end
        end
    end,
})

-- No Fog
local NoFogConnection
local StoredAtmospheres = {}

Tabs.Visuals:Toggle({
    Title = "No Fog",
    Description = "Mengurangi efek kabut dan Atmosphere",
    Value = false,

    Callback = function(Value)
        if NoFogConnection then
            NoFogConnection:Disconnect()
            NoFogConnection = nil
        end

        if Value then
            Lighting.FogStart = 0
            Lighting.FogEnd = 999999

            StoredAtmospheres = {}

            for _, Object in ipairs(Lighting:GetChildren()) do
                if Object:IsA("Atmosphere") then
                    table.insert(StoredAtmospheres, Object)
                    Object.Parent = nil
                end
            end

            NoFogConnection = Lighting.ChildAdded:Connect(function(Object)
                if Object:IsA("Atmosphere") then
                    table.insert(StoredAtmospheres, Object)
                    Object.Parent = nil
                end
            end)
        else
            for _, Object in ipairs(StoredAtmospheres) do
                if Object and Object.Parent == nil then
                    Object.Parent = Lighting
                end
            end

            StoredAtmospheres = {}
        end
    end,
})

Tabs.Visuals:Toggle({
    Title = "Max Zoom ",
    Description = "Memperluas jarak maksimal zoom kamera",
    Value = false,
    Callback = function(Value)
        if Value then
            LocalPlayer.CameraMaxZoomDistance = 999999
        else
            LocalPlayer.CameraMaxZoomDistance = 400 -- Mengembalikan ke standar Roblox
        end
    end,
})

-- Tambahkan kode ini ke bagian bawah (misalnya di dalam tab Settings atau Visuals)

-- Tabel untuk menyimpan data asli sebelum diubah
local OriginalGraphics = {
    Lighting = {},
    Parts = {},
    Effects = {},
    Terrain = {}
}

Tabs.Visuals:Slider({
    Title = "FOV",
    Description = "Mengatur luas sudut pandang kamera",
    Value = {
        Min = 70,
        Max = 120,
        Default = 70,
    },
    Callback = function(Value)
        workspace.CurrentCamera.FieldOfView = Value
    end,
})

Tabs.Visuals:Toggle({
    Title = "FPS Booster",
    Description = "Meningkatkan FPS dan bisa dikembalikan ke semula saat dimatikan",
    Value = false,
    Callback = function(Value)
        local Lighting = game:GetService("Lighting")
        local Terrain = workspace:FindFirstChildOfClass("Terrain")
        
        if Value then
            -- 1. Simpan dan ubah setting Lighting
            pcall(function()
                OriginalGraphics.Lighting.GlobalShadows = Lighting.GlobalShadows
                OriginalGraphics.Lighting.FogEnd = Lighting.FogEnd
                
                Lighting.GlobalShadows = false
                Lighting.FogEnd = 9e9
            end)
            
            for _, v in ipairs(Lighting:GetChildren()) do
                pcall(function()
                    if v:IsA("PostEffect") or v:IsA("Atmosphere") or v:IsA("Sky") then
                        OriginalGraphics.Effects[v] = v.Enabled
                        v.Enabled = false
                    end
                end)
            end
            
            -- 2. Simpan dan ubah Terrain
            if Terrain then
                pcall(function()
                    OriginalGraphics.Terrain.WaterWaveSize = Terrain.WaterWaveSize
                    OriginalGraphics.Terrain.WaterWaveTransparency = Terrain.WaterWaveTransparency
                    OriginalGraphics.Terrain.WaterTransparency = Terrain.WaterTransparency
                    OriginalGraphics.Terrain.WaterReflectance = Terrain.WaterReflectance
                    
                    Terrain.WaterWaveSize = 0
                    Terrain.WaterWaveTransparency = 1
                    Terrain.WaterTransparency = 1
                    Terrain.WaterReflectance = 0
                end)
            end
            
            -- 3. Simpan dan ubah Part, Decal, Particle
            for _, v in ipairs(workspace:GetDescendants()) do
                pcall(function()
                    if v:IsA("BasePart") then
                        local isCharacterPart = v.Parent and v.Parent:FindFirstChildOfClass("Humanoid")
                        if not isCharacterPart then
                            OriginalGraphics.Parts[v] = {
                                Material = v.Material,
                                Reflectance = v.Reflectance
                            }
                            v.Material = Enum.Material.SmoothPlastic
                            v.Reflectance = 0
                        end
                    elseif v:IsA("Decal") or v:IsA("Texture") then
                        OriginalGraphics.Parts[v] = { Transparency = v.Transparency }
                        v.Transparency = 1
                    elseif v:IsA("ParticleEmitter") or v:IsA("Fire") or v:IsA("Smoke") or v:IsA("Sparkles") then
                        OriginalGraphics.Parts[v] = { Enabled = v.Enabled }
                        v.Enabled = false
                    end
                end)
            end
            
            pcall(function()
                WindUI:Notify({
                    Title = "FPS Booster",
                    Content = "FPS Booster",
                    Duration = 3,
                })
            end)
        else
            -- 4. Kembalikan semua setting ke semula saat toggle dimatikan
            pcall(function()
                if OriginalGraphics.Lighting.GlobalShadows ~= nil then
                    Lighting.GlobalShadows = OriginalGraphics.Lighting.GlobalShadows
                end
                if OriginalGraphics.Lighting.FogEnd ~= nil then
                    Lighting.FogEnd = OriginalGraphics.Lighting.FogEnd
                end
            end)
            
            for obj, state in pairs(OriginalGraphics.Effects) do
                pcall(function()
                    if obj and obj.Parent then
                        obj.Enabled = state
                    end
                end)
            end
            
            if Terrain then
                pcall(function()
                    if OriginalGraphics.Terrain.WaterWaveSize ~= nil then
                        Terrain.WaterWaveSize = OriginalGraphics.Terrain.WaterWaveSize
                        Terrain.WaterWaveTransparency = OriginalGraphics.Terrain.WaterWaveTransparency
                        Terrain.WaterTransparency = OriginalGraphics.Terrain.WaterTransparency
                        Terrain.WaterReflectance = OriginalGraphics.Terrain.WaterReflectance
                    end
                end)
            end
            
            for obj, data in pairs(OriginalGraphics.Parts) do
                pcall(function()
                    if obj and obj.Parent then
                        if obj:IsA("BasePart") then
                            obj.Material = data.Material
                            obj.Reflectance = data.Reflectance
                        elseif obj:IsA("Decal") or obj:IsA("Texture") then
                            obj.Transparency = data.Transparency
                        elseif obj:IsA("ParticleEmitter") or obj:IsA("Fire") or obj:IsA("Smoke") or obj:IsA("Sparkles") then
                            obj.Enabled = data.Enabled
                        end
                    end
                end)
            end
            
            pcall(function()
                WindUI:Notify({
                    Title = "FPS Booster",
                    Content = "FPS booster",
                    Duration = 3,
                })
            end)
        end
    end,
})

-- Contoh cara pakai Section di WindUI
Tabs.Visuals:Section({ Title = "MODE Player ESP" })

_G.HitboxEnabled = false
_G.HitboxSize = 5 -- Ukuran default

-- Slider untuk mengatur ukuran lebar/besar Hitbox
Tabs.Visuals:Slider({
    Title = "Hitbox",
    Description = "Mengatur ukuran hitbox player lain",
    Value = {
        Min = 2,
        Max = 60,
        Default = 5,
    },
    Callback = function(Value)
        _G.HitboxSize = Value
    end,
})

-- Toggle untuk mengaktifkan/mematikan Hitbox Outline Merah
Tabs.Visuals:Toggle({
    Title = "Player Hitbox",
    Description = "Memperbesar hitbox dengan garis merah di pinggir & dalam transparan",
    Value = false,
    Callback = function(Value)
        _G.HitboxEnabled = Value
        
        if _G.HitboxConnections then
            for _, conn in ipairs(_G.HitboxConnections) do
                conn:Disconnect()
            end
            _G.HitboxConnections = nil
        end
        
        local function applyHitbox(player, character)
            if player == LocalPlayer then return end
            local rootPart = character:FindFirstChild("HumanoidRootPart")
            if not rootPart then return end
            
            -- Pakai SelectionBox supaya bagian tengahnya transparan/invisible, 
            -- dan pinggirannya ada garis warna merah yang jelas.
            local selectionBox = rootPart:FindFirstChild("IrwanHitboxOutline")
            if not selectionBox then
                selectionBox = Instance.new("SelectionBox")
                selectionBox.Name = "IrwanHitboxOutline"
                selectionBox.Adornee = rootPart
                -- Warna garis pinggir merah menyala
                selectionBox.Color3 = Color3.fromRGB(255, 0, 0)
                -- Transparansi pinggiran (0 = jelas, 1 = invisible)
                selectionBox.LineThickness = 0.05
                selectionBox.Transparency = 0.2
                selectionBox.Parent = rootPart
            end
            
            -- Loop untuk mengubah ukuran fisik hitbox secara real-time
            task.spawn(function()
                while _G.HitboxEnabled and character and character.Parent and rootPart.Parent do
                    local size = Vector3.new(_G.HitboxSize, _G.HitboxSize, _G.HitboxSize)
                    rootPart.Size = size
                    
                    -- Matikan benturan fisik supaya pemain tidak mental (tetap bisa ditembak/diserang)
                    rootPart.CanCollide = false
                    task.wait(0.2)
                end
                
                -- Kembalikan ke ukuran normal saat dimatikan
                if not _G.HitboxEnabled then
                    pcall(function()
                        rootPart.Size = Vector3.new(2, 2, 1) -- Ukuran standar Roblox
                        if selectionBox then selectionBox:Destroy() end
                    end)
                end
            end)
        end

        if Value then
            _G.HitboxConnections = {}
            
            -- Terapkan ke player yang sudah ada di server
            for _, player in ipairs(Players:GetPlayers()) do
                if player.Character then
                    applyHitbox(player, player.Character)
                end
                table.insert(_G.HitboxConnections, player.CharacterAdded:Connect(function(char)
                    task.wait(1)
                    applyHitbox(player, char)
                end))
            end
            
            -- Tangkap player baru yang join
            table.insert(_G.HitboxConnections, Players.PlayerAdded:Connect(function(player)
                table.insert(_G.HitboxConnections, player.CharacterAdded:Connect(function(char)
                    task.wait(1)
                    applyHitbox(player, char)
                end))
            end))
        else
            -- Bersihkan semua saat dimatikan
            for _, player in ipairs(Players:GetPlayers()) do
                if player.Character then
                    local root = player.Character:FindFirstChild("HumanoidRootPart")
                    if root then
                        root.Size = Vector3.new(2, 2, 1)
                        local selectionBox = root:FindFirstChild("IrwanHitboxOutline")
                        if selectionBox then selectionBox:Destroy() end
                    end
                end
            end
        end
    end,
})

Tabs.Visuals:Toggle({
    Title = "Player ESP",
    Description = "Menampilkan nama berwarna unik dan HP player di atas kepala",
    Value = false,
    Callback = function(Value)
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer

        -- Bersihkan koneksi lama kalau ada
        if _G.ProESPConnections then
            for _, conn in ipairs(_G.ProESPConnections) do
                conn:Disconnect()
            end
            _G.ProESPConnections = nil
        end

        local function getPlayerColor(player)
            if player.Team then
                return player.Team.TeamColor.Color
            else
                local hash = 0
                for i = 1, #player.Name do
                    hash = player.Name:byte(i) + (hash * 31)
                end
                math.randomseed(hash)
                return Color3.fromHSV(math.random(), 0.8, 1)
            end
        end

        local function createESP(player, character)
            if player == LocalPlayer then return end
            local head = character:WaitForChild("Head", 5)
            local humanoid = character:WaitForChild("Humanoid", 5)
            if not head or not humanoid then return end
            
            if head:FindFirstChild("IrwanProESP") then return end
            
            local billboard = Instance.new("BillboardGui")
            billboard.Name = "IrwanProESP"
            billboard.Size = UDim2.new(0, 200, 0, 60)
            billboard.StudsOffset = Vector3.new(0, 2.8, 0)
            billboard.AlwaysOnTop = true
            billboard.Parent = head
            
            local nameLabel = Instance.new("TextLabel")
            nameLabel.Size = UDim2.new(1, 0, 0, 25)
            nameLabel.BackgroundTransparency = 1
            nameLabel.Text = player.Name
            nameLabel.TextColor3 = getPlayerColor(player)
            nameLabel.TextSize = 14
            nameLabel.Font = Enum.Font.SourceSansBold
            nameLabel.TextStrokeTransparency = 0
            nameLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
            nameLabel.Parent = billboard
            
            local hpLabel = Instance.new("TextLabel")
            hpLabel.Size = UDim2.new(1, 0, 0, 20)
            hpLabel.Position = UDim2.new(0, 0, 0, 22)
            hpLabel.BackgroundTransparency = 1
            hpLabel.TextColor3 = Color3.fromRGB(0, 255, 100)
            hpLabel.TextSize = 12
            hpLabel.Font = Enum.Font.SourceSansBold
            hpLabel.TextStrokeTransparency = 0
            hpLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
            hpLabel.Parent = billboard
            
            local hpConn
            hpConn = humanoid.HealthChanged:Connect(function(health)
                if hpLabel and hpLabel.Parent then
                    hpLabel.Text = "HP: " .. math.floor(health) .. " / " .. math.floor(humanoid.MaxHealth)
                    if health <= (humanoid.MaxHealth * 0.25) then
                        hpLabel.TextColor3 = Color3.fromRGB(255, 50, 50)
                    else
                        hpLabel.TextColor3 = Color3.fromRGB(0, 255, 100)
                    end
                else
                    hpConn:Disconnect()
                end
            end)
            
            hpLabel.Text = "HP: " .. math.floor(humanoid.Health) .. " / " .. math.floor(humanoid.MaxHealth)
        end

        local function removeESP(character)
            local head = character and character:FindFirstChild("Head")
            if head then
                local esp = head:FindFirstChild("IrwanProESP")
                if esp then
                    esp:Destroy()
                end
            end
        end

        if Value then
            _G.ProESPConnections = {}

            -- Pasang ke player yang sudah ada
            for _, player in ipairs(Players:GetPlayers()) do
                if player.Character then
                    createESP(player, player.Character)
                end
                table.insert(_G.ProESPConnections, player.CharacterAdded:Connect(function(char)
                    createESP(player, char)
                end))
            end

            -- Tangkap player baru yang join
            table.insert(_G.ProESPConnections, Players.PlayerAdded:Connect(function(player)
                table.insert(_G.ProESPConnections, player.CharacterAdded:Connect(function(char)
                    createESP(player, char)
                end))
            end))
        else
            -- Hapus semua GUI ESP yang sedang nempel di player
            for _, player in ipairs(Players:GetPlayers()) do
                if player.Character then
                    removeESP(player.Character)
                end
            end
        end
    end,
})

Tabs.Visuals:Toggle({
    Title = "Player BOX ESP",
    Description = "Menampilkan wujud badan player menembus tembok tanpa efek cahaya",
    Value = false,
    Callback = function(Value)
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer

        if _G.SolidESPConnections then
            for _, conn in ipairs(_G.SolidESPConnections) do
                conn:Disconnect()
            end
            _G.SolidESPConnections = nil
        end

        local function getPlayerColor(player)
            if player.Team then
                return player.Team.TeamColor.Color
            else
                local hash = 0
                for i = 1, #player.Name do
                    hash = player.Name:byte(i) + (hash * 31)
                end
                math.randomseed(hash)
                return Color3.fromHSV(math.random(), 0.8, 1)
            end
        end

        local function applySolidESP(player, character)
            if player == LocalPlayer then return end
            
            local playerColor = getPlayerColor(player)
            
            for _, part in ipairs(character:GetDescendants()) do
                if part:IsA("BasePart") and part.Name ~= "HumanoidRootPart" then
                    -- Buat duplikat part secara virtual atau pasang BoxAdornment tipis yang ngepas di tiap bagian badan (Kepala, Torso, Tangan, Kaki)
                    if not part:FindFirstChild("IrwanSolidPart") then
                        local adorn = Instance.new("BoxHandleAdornment")
                        adorn.Name = "IrwanSolidPart"
                        adorn.Adornee = part
                        adorn.AlwaysOnTop = true
                        adorn.ZIndex = 5
                        adorn.Size = part.Size + Vector3.new(0.05, 0.05, 0.05)
                        adorn.Color3 = playerColor
                        adorn.Transparency = 0.4 -- Semi transparan, kelihatan solid tanpa efek cahaya silau
                        adorn.Parent = part
                    end
                end
            end
        end

        local function removeSolidESP(character)
            if character then
                for _, part in ipairs(character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        local adorn = part:FindFirstChild("IrwanSolidPart")
                        if adorn then
                            adorn:Destroy()
                        end
                    end
                end
            end
        end

        if Value then
            _G.SolidESPConnections = {}

            for _, player in ipairs(Players:GetPlayers()) do
                if player.Character then
                    applySolidESP(player, player.Character)
                end
                table.insert(_G.SolidESPConnections, player.CharacterAdded:Connect(function(char)
                    task.wait(1) -- Tunggu karakter load sempurna
                    applySolidESP(player, char)
                end))
            end

            table.insert(_G.SolidESPConnections, Players.PlayerAdded:Connect(function(player)
                table.insert(_G.SolidESPConnections, player.CharacterAdded:Connect(function(char)
                    task.wait(1)
                    applySolidESP(player, char)
                end))
            end))
        else
            for _, player in ipairs(Players:GetPlayers()) do
                if player.Character then
                    removeSolidESP(player.Character)
                end
            end
        end
    end,
})

Tabs.Visuals:Toggle({
    Title = "Player Tool ESP",
    Description = "Menampilkan nama dan tool yang sedang dipegang player",
    Value = false,
    Callback = function(Value)
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer

        -- Bersihkan koneksi lama kalau ada
        if _G.ToolESPConnections then
            for _, conn in ipairs(_G.ToolESPConnections) do
                conn:Disconnect()
            end
            _G.ToolESPConnections = nil
        end

        local function getPlayerColor(player)
            if player.Team then
                return player.Team.TeamColor.Color
            else
                local hash = 0
                for i = 1, #player.Name do
                    hash = player.Name:byte(i) + (hash * 31)
                end
                math.randomseed(hash)
                return Color3.fromHSV(math.random(), 0.8, 1)
            end
        end

        local function createESP(player, character)
            if player == LocalPlayer then return end
            local head = character:WaitForChild("Head", 5)
            if not head then return end
            
            if head:FindFirstChild("IrwanToolESP") then return end
            
            local billboard = Instance.new("BillboardGui")
            billboard.Name = "IrwanToolESP"
            billboard.Size = UDim2.new(0, 200, 0, 50)
            billboard.StudsOffset = Vector3.new(0, 2.8, 0)
            billboard.AlwaysOnTop = true
            billboard.Parent = head
            
            -- Label Nama Player
            local nameLabel = Instance.new("TextLabel")
            nameLabel.Size = UDim2.new(1, 0, 0, 25)
            nameLabel.BackgroundTransparency = 1
            nameLabel.Text = player.Name
            nameLabel.TextColor3 = getPlayerColor(player)
            nameLabel.TextSize = 14
            nameLabel.Font = Enum.Font.SourceSansBold
            nameLabel.TextStrokeTransparency = 0
            nameLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
            nameLabel.Parent = billboard
            
            -- Label Tool / Item yang sedang dipegang
            local toolLabel = Instance.new("TextLabel")
            toolLabel.Size = UDim2.new(1, 0, 0, 20)
            toolLabel.Position = UDim2.new(0, 0, 0, 22)
            toolLabel.BackgroundTransparency = 1
            toolLabel.TextColor3 = Color3.fromRGB(255, 220, 0) -- Warna kuning emas buat item
            toolLabel.TextSize = 12
            toolLabel.Font = Enum.Font.SourceSansBold
            toolLabel.TextStrokeTransparency = 0
            toolLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
            toolLabel.Text = "Holding: None"
            toolLabel.Parent = billboard
            
            -- Fungsi buat ngecek tool yang lagi dipegang di tangan
            local function updateTool()
                if not toolLabel or not toolLabel.Parent then return end
                local equippedTool = "None"
                
                for _, item in ipairs(character:GetChildren()) do
                    if item:IsA("Tool") then
                        equippedTool = item.Name
                        break
                    end
                end
                
                if equippedTool ~= "None" then
                    toolLabel.Text = "[" .. equippedTool .. "]"
                    toolLabel.TextColor3 = Color3.fromRGB(0, 255, 150) -- Hijau kalau megang tool
                else
                    toolLabel.Text = "Holding: None"
                    toolLabel.TextColor3 = Color3.fromRGB(180, 180, 180) -- Abu-abu kalau kosong
                end
            end

            -- Pantau perubahan item di karakter
            local childConn = character.ChildAdded:Connect(updateTool)
            local childRemConn = character.ChildRemoved:Connect(updateTool)
            
            -- Simpan referensi koneksi lokal biar bisa dibersihkan nanti
            character.AncestryChanged:Connect(function()
                if not character.Parent then
                    if childConn then childConn:Disconnect() end
                    if childRemConn then childRemConn:Disconnect() end
                end
            end)

            updateTool()
        end

        local function removeESP(character)
            local head = character and character:FindFirstChild("Head")
            if head then
                local esp = head:FindFirstChild("IrwanToolESP")
                if esp then
                    esp:Destroy()
                end
            end
        end

        if Value then
            _G.ToolESPConnections = {}

            -- Pasang ke player yang sudah ada di server
            for _, player in ipairs(Players:GetPlayers()) do
                if player.Character then
                    createESP(player, player.Character)
                end
                table.insert(_G.ToolESPConnections, player.CharacterAdded:Connect(function(char)
                    createESP(player, char)
                end))
            end

            -- Tangkap player baru yang join
            table.insert(_G.ToolESPConnections, Players.PlayerAdded:Connect(function(player)
                table.insert(_G.ToolESPConnections, player.CharacterAdded:Connect(function(char)
                    createESP(player, char)
                end))
            end))
        else
            -- Hapus semua GUI ESP yang nempel di player saat dimatiin
            for _, player in ipairs(Players:GetPlayers()) do
                if player.Character then
                    removeESP(player.Character)
                end
            end
        end
    end,
})

-- Contoh cara pakai Section di WindUI
Tabs.Visuals:Section({ Title = "MODE NPC ESP" })

Tabs.Visuals:Toggle({
    Title = "NPC ESP",
    Description = "Menampilkan nama NPC di atas kepala menembus dinding",
    Value = false,
    Callback = function(Value)
        local Workspace = game:GetService("Workspace")
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer

        if _G.NPCNameConnections then
            for _, conn in ipairs(_G.NPCNameConnections) do
                conn:Disconnect()
            end
            _G.NPCNameConnections = nil
        end

        local function isNPC(model)
            if not model:IsA("Model") then return false end
            local humanoid = model:FindFirstChildOfClass("Humanoid")
            local head = model:FindFirstChild("Head") or model:FindFirstChild("HumanoidRootPart")
            
            if humanoid and head then
                for _, player in ipairs(Players:GetPlayers()) do
                    if player.Character == model then
                        return false
                    end
                end
                return true
            end
            return false
        end

        local function createNPCName(character)
            if not isNPC(character) then return end
            local head = character:FindFirstChild("Head") or character:FindFirstChild("HumanoidRootPart")
            if not head then return end
            
            if head:FindFirstChild("IrwanNPCNameTag") then return end
            
            local billboard = Instance.new("BillboardGui")
            billboard.Name = "IrwanNPCNameTag"
            billboard.Size = UDim2.new(0, 200, 0, 30)
            billboard.StudsOffset = Vector3.new(0, 2.5, 0)
            billboard.AlwaysOnTop = true
            billboard.Parent = head
            
            local nameLabel = Instance.new("TextLabel")
            nameLabel.Size = UDim2.new(1, 0, 1, 0)
            nameLabel.BackgroundTransparency = 1
            nameLabel.Text = "[NPC] " .. character.Name
            nameLabel.TextColor3 = Color3.fromRGB(255, 140, 0) -- Warna oranye khas NPC
            nameLabel.TextSize = 13
            nameLabel.Font = Enum.Font.SourceSansBold
            nameLabel.TextStrokeTransparency = 0
            nameLabel.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
            nameLabel.Parent = billboard
        end

        local function removeNPCName(character)
            if character then
                local head = character:FindFirstChild("Head") or character:FindFirstChild("HumanoidRootPart")
                if head then
                    local tag = head:FindFirstChild("IrwanNPCNameTag")
                    if tag then
                        tag:Destroy()
                    end
                end
            end
        end

        if Value then
            _G.NPCNameConnections = {}

            -- Scan NPC yang sudah ada di Workspace
            for _, obj in ipairs(Workspace:GetDescendants()) do
                if isNPC(obj) then
                    createNPCName(obj)
                end
            end

            -- Tangkap NPC baru yang muncul
            table.insert(_G.NPCNameConnections, Workspace.DescendantAdded:Connect(function(descendant)
                task.wait(0.5)
                if descendant:IsA("Model") and isNPC(descendant) then
                    createNPCName(descendant)
                end
            end))
        else
            -- Bersihkan semua nama NPC saat dimatiin
            for _, obj in ipairs(Workspace:GetDescendants()) do
                if isNPC(obj) then
                    removeNPCName(obj)
                end
            end
        end
    end,
})

Tabs.Visuals:Toggle({
    Title = "NPC BOX ESP",
    Description = "Menampilkan wujud badan NPC menembus tembok tanpa efek cahaya",
    Value = false,
    Callback = function(Value)
        local Workspace = game:GetService("Workspace")
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer

        if _G.NPCObjConnections then
            for _, conn in ipairs(_G.NPCObjConnections) do
                conn:Disconnect()
            end
            _G.NPCObjConnections = nil
        end

        local function isNPC(model)
            -- Pastikan model punya Humanoid, bukan Player, dan punya RootPart/Head
            if not model:IsA("Model") then return false end
            local humanoid = model:FindFirstChildOfClass("Humanoid")
            local rootPart = model:FindFirstChild("HumanoidRootPart") or model:FindFirstChild("Torso") or model:FindFirstChild("UpperTorso")
            
            if humanoid and rootPart then
                -- Cek apakah model ini player yang sedang login
                for _, player in ipairs(Players:GetPlayers()) do
                    if player.Character == model then
                        return false
                    end
                end
                return true
            end
            return false
        end

        local function applyNPCObjESP(character)
            if not isNPC(character) then return end
            
            -- Warna khusus NPC (Contoh: Oranye terang)
            local npcColor = Color3.fromRGB(255, 140, 0)
            
            for _, part in ipairs(character:GetDescendants()) do
                if part:IsA("BasePart") then
                    if not part:FindFirstChild("IrwanNPCSolidPart") then
                        local adorn = Instance.new("BoxHandleAdornment")
                        adorn.Name = "IrwanNPCSolidPart"
                        adorn.Adornee = part
                        adorn.AlwaysOnTop = true
                        adorn.ZIndex = 5
                        adorn.Size = part.Size + Vector3.new(0.05, 0.05, 0.05)
                        adorn.Color3 = npcColor
                        adorn.Transparency = 0.4 -- Semi transparan, kelihatan solid
                        adorn.Parent = part
                    end
                end
            end
        end

        local function removeNPCObjESP(character)
            if character then
                for _, part in ipairs(character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        local adorn = part:FindFirstChild("IrwanNPCSolidPart")
                        if adorn then
                            adorn:Destroy()
                        end
                    end
                end
            end
        end

        if Value then
            _G.NPCObjConnections = {}

            -- Scan NPC yang sudah ada di Workspace (biasanya di dalam folder khusus atau Workspace langsung)
            for _, obj in ipairs(Workspace:GetDescendants()) do
                if isNPC(obj) then
                    applyNPCObjESP(obj)
                end
            end

            -- Tangkap NPC baru yang muncul/spawn
            table.insert(_G.NPCObjConnections, Workspace.DescendantAdded:Connect(function(descendant)
                task.wait(0.5) -- Tunggu sebentar sampai model load sempurna
                if descendant:IsA("Model") and isNPC(descendant) then
                    applyNPCObjESP(descendant)
                end
            end))
        else
            -- Bersihkan semua ESP NPC saat dimatiin
            for _, obj in ipairs(Workspace:GetDescendants()) do
                if isNPC(obj) then
                    removeNPCObjESP(obj)
                end
            end
        end
    end,
})

--==================================================
-- SCRIPT
--==================================================

Tabs.Script:Button({
    Title = "Infinite Yield",
    Description = "Admin commands universal paling populer",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source"))()
    end,
})

Tabs.Script:Button({
    Title = "Scriptblox",
    Description = "scriptblox  paling populer",
    Callback = function()
         loadstring(game:HttpGet("https://raw.githubusercontent.com/panchooo677/Btbthy/refs/heads/main/README.md"))() 
    end,
})

Tabs.Script:Button({
    Title = "Cmdbar2",
    Description = "Cmdbar2  paling populer",
    Callback = function()
         loadstring(game:HttpGet("https://pastebin.com/raw/2kWYe98f"))()
    end,
})

Tabs.Script:Button({
    Title = "ServerBrowser",
    Description = "ServerBrowser  paling populer",
    Callback = function()
         loadstring(game:HttpGet("https://raw.githubusercontent.com/RealBatu20/AI-Scripts-2025/refs/heads/main/ServerBrowserImproved.lua"))() 
    end,
})

Tabs.Script:Button({
    Title = "FLY GUI V3",
    Description = "FLY GUI V3paling populer",
    Callback = function()
         loadstring(game:HttpGet("https://raw.githubusercontent.com/XNEOFF/FlyGuiV3/main/FlyGuiV3.txt"))()
    end,
})

Tabs.Script:Button({
    Title = "SimpleSpy",
    Description = "SimpleSpy  paling populer",
    Callback = function()
         loadstring(game:HttpGet("https://raw.githubusercontent.com/infyiff/backup/main/SimpleSpyV3/main.lua"))()
    end,
})

--==================================================
-- TOOL
--==================================================

Tabs.Tool:Button({
    Title = "Give Click TP Tool",
    Description = "Teleport hanya bekerja jika kursor mengarah ke blok/objek",
    Callback = function()
        local Backpack = LocalPlayer:FindFirstChildOfClass("Backpack")
        local Character = LocalPlayer.Character
        
        -- Hapus tool lama kalau masih ada
        if Backpack then
            local Old = Backpack:FindFirstChild("Click TP")
            if Old then Old:Destroy() end
        end
        if Character then
            local Old = Character:FindFirstChild("Click TP")
            if Old then Old:Destroy() end
        end
        
        -- Buat Tool baru
        local ClickTPTool = Instance.new("Tool")
        ClickTPTool.Name = "Click TP"
        ClickTPTool.RequiresHandle = false
        ClickTPTool.CanBeDropped = false
        
        ClickTPTool.Activated:Connect(function()
            local Mouse = LocalPlayer:GetMouse()
            local TargetPart = Mouse.Target -- Mendeteksi objek yang disentuh kursor
            local Root = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
            
            -- Validasi: Jika tidak ada part yang diklik (klik udara kosong/langit)
            if not TargetPart or not Mouse.Hit then
                WindUI:Notify({
                    Title = "Click TP",
                    Content = "NO blok",
                    Duration = 2,
                })
                return
            end
            
            -- Jika ada blok yang diklik, lakukan teleport ke posisi tersebut
            if Root then
                Root.CFrame = CFrame.new(Mouse.Hit.Position + Vector3.new(0, 3, 0))
                
                WindUI:Notify({
                    Title = "Click TP",
                    Content = "Click TP",
                    Duration = 2,
                })
            end
        end)
        
        ClickTPTool.Parent = Backpack
        
        WindUI:Notify({
            Title = "Click TP",
            Content = "Tool Click TP",
            Duration = 3,
        })
    end,
})

-- Tambahkan kode ini ke dalam tab Tool atau Script di WindUI lu

Tabs.Tool:Button({
    Title = "Give Tool NPC Robot",
    Description = "Membuat tool yang membuat NPC guling-guling atau nge-glitch saat diklik",
    Callback = function()
        local Backpack = LocalPlayer:FindFirstChildOfClass("Backpack")
        local Character = LocalPlayer.Character

        -- Hapus tool lama kalau masih ada biar nggak numpuk
        if Backpack then
            local Old = Backpack:FindFirstChild("NPC Roll Tool")
            if Old then Old:Destroy() end
        end
        if Character then
            local Old = Character:FindFirstChild("NPC Roll Tool")
            if Old then Old:Destroy() end
        end

        -- Buat Tool baru
        local RollTool = Instance.new("Tool")
        RollTool.Name = "NPC Roll Tool"
        RollTool.RequiresHandle = false
        RollTool.CanBeDropped = false

        -- Fungsi saat tool diklik/diaktifkan
        RollTool.Activated:Connect(function()
            local Mouse = LocalPlayer:GetMouse()
            local TargetPart = Mouse.Target

            if not TargetPart then
                WindUI:Notify({
                    Title = "NPC Roll Tool",
                    Content = "Arahkan kursor ke tubuh NPC!",
                    Duration = 2,
                })
                return
            end

            -- Cari model utama dari part yang diklik
            local hitModel = TargetPart:FindFirstAncestorWhichIsA("Model")

            if not hitModel then
                WindUI:Notify({
                    Title = "NPC Roll Tool",
                    Content = "Yang kamu klik bukan NPC!",
                    Duration = 2,
                })
                return
            end

            local LocalChar = LocalPlayer.Character

            -- Validasi: Pastikan bukan diri sendiri atau player lain
            if LocalChar and hitModel == LocalChar then return end

            local isPlayer = false
            for _, p in ipairs(Players:GetPlayers()) do
                if p.Character == hitModel then
                    isPlayer = true
                    break
                end
            end

            if isPlayer then
                WindUI:Notify({
                    Title = "NPC Roll Tool",
                    Content = "Tidak bisa digunakan ke Player lain!",
                    Duration = 2,
                })
                return
            end

            local humanoid = hitModel:FindFirstChildOfClass("Humanoid")
            if not humanoid or humanoid.Health <= 0 then
                WindUI:Notify({
                    Title = "NPC Robot Tool",
                    Content = "NPC",
                    Duration = 2,
                })
                return
            end

            -- Terapkan efek glitch / guling-guling persis seperti logika script Discord temanmu
            pcall(function()
                humanoid.WalkSpeed = 0
                humanoid.JumpPower = 0
                humanoid.AutoRotate = false
                humanoid.PlatformStand = true
                
                pcall(function()
                    humanoid:SetStateEnabled(Enum.HumanoidStateType.Running, false)
                    humanoid:SetStateEnabled(Enum.HumanoidStateType.RunningNoPhysics, false)
                    humanoid:SetStateEnabled(Enum.HumanoidStateType.Jumping, false)
                    humanoid:SetStateEnabled(Enum.HumanoidStateType.GettingUp, false)
                    humanoid:ChangeState(Enum.HumanoidStateType.Physics)
                end)
            end)

            -- Berikan putaran/gulingan acak pada bagian tubuh NPC
            for _, part in ipairs(hitModel:GetDescendants()) do
                if part:IsA("BasePart") then
                    pcall(function()
                        part.AssemblyAngularVelocity = Vector3.new(
                            math.random(-20, 20),
                            math.random(-20, 20),
                            math.random(-20, 20)
                        )
                        part.AssemblyLinearVelocity = Vector3.new(
                            math.random(-15, 15),
                            math.random(5, 25),
                            math.random(-15, 15)
                        )
                    end)
                end
            end

            WindUI:Notify({
                Title = "NPC Robot Tool",
                Content = "NPC " .. hitModel.Name .. "Robot",
                Duration = 3,
            })
        end)

        RollTool.Parent = Backpack

        pcall(function()
            WindUI:Notify({
                Title = "NPC Robot Tool",
                Content = "Tool NPC Robot",
                Duration = 3,
            })
        end)
    end,
})

Tabs.Tool:Button({
    Title = "Fake Gamepass V5",
    Description = "Fake Gamepass V5 universal paling populer",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Ahma174/Fake-Gamepasses/refs/heads/main/V5"))()
    end,
})

--==================================================
-- HACKER
--==================================================

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

_G.SpectateTargetName = "Pilih Player"
_G.IsSpectating = false

local function getPlayerList()
    local list = {"Pilih Player"}
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer then
            table.insert(list, p.Name)
        end
    end
    return list
end

-- 1. Variabel Global untuk Spectate
_G.SpectateTargetName = "Pilih Player"
_G.IsSpectating = false

-- 2. Fungsi untuk mengambil daftar player terbaru di server
local function getPlayerList()
    local list = {"Pilih Player"}
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer then
            table.insert(list, p.Name)
        end
    end
    return list
end

-- 3. Dropdown untuk memilih player yang ingin di-spectate
local SpectateDropdown = Tabs.hacker:Dropdown({
    Title = "Spectate Player",
    Description = "Pilih player untuk melihat pandangan kamera mereka (Auto Update)",
    Values = getPlayerList(),
    Default = "Pilih Player",
    Callback = function(Selected)
        _G.SpectateTargetName = Selected
        
        local targetPlayer = Players:FindFirstChild(_G.SpectateTargetName)
        if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
            _G.IsSpectating = true
            Camera.CameraSubject = targetPlayer.Character:FindFirstChildOfClass("Humanoid")
        else
            _G.IsSpectating = false
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
                Camera.CameraSubject = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
            end
        end
    end,
})

-- 4. Auto-update dropdown saat ada player baru masuk server
Players.PlayerAdded:Connect(function()
    pcall(function()
        SpectateDropdown:Refresh(getPlayerList())
    end)
end)

-- 5. Auto-update & pengaman saat ada player keluar server
Players.PlayerRemoving:Connect(function(leavingPlayer)
    pcall(function()
        -- Kalau player yang lagi lu intai ternyata keluar, otomatis kembalikan kamera ke diri sendiri
        if _G.SpectateTargetName == leavingPlayer.Name then
            _G.SpectateTargetName = "Pilih Player"
            _G.IsSpectating = false
            if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
                Camera.CameraSubject = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
            end
        end
        -- Refresh ulang daftar nama di dropdown
        SpectateDropdown:Refresh(getPlayerList())
    end)
end)

-- Contoh cara pakai Section di WindUI
Tabs.hacker:Section({ Title = "Hyper Laser Gun Lock" })

local TargetDropdown = Tabs.hacker:Dropdown({
    Title = "Pilih Target Lock",
    Description = "Pilih nama player yang ingin dikunci pelurunya",
    Values = getPlayerList(),
    Default = "Pilih Player",
    Callback = function(Selected)
        _G.LockedTargetName = Selected
    end,
})

Players.PlayerAdded:Connect(function()
    pcall(function()
        TargetDropdown:Refresh(getPlayerList())
    end)
end)

Players.PlayerRemoving:Connect(function(leavingPlayer)
    pcall(function()
        if _G.LockedTargetName == leavingPlayer.Name then
            _G.LockedTargetName = "Pilih Player"
        end
        TargetDropdown:Refresh(getPlayerList())
    end)
end)

-- Fungsi untuk mengecek apakah player sedang memegang senjata/gun laser
local function isHoldingGun()
    local char = LocalPlayer.Character
    if not char then return false end
    local tool = char:FindFirstChildOfClass("Tool")
    if tool then
        local name = tool.Name:lower()
        -- Deteksi nama senjata atau laser
        if name:find("laser") or name:find("gun") or name:find("palu") or name:find("tool") then
            return true
        end
    end
    return false
end

Tabs.hacker:Toggle({
    Title = "Hyper Laser Gun Tp",
    Description = "Teleport peluru aktif, fungsi sudah dihapus",
    Value = false,
    Callback = function(Value)
        _G.WeaponEnabled = Value
        
        if _G.WeaponConnection then
            _G.WeaponConnection:Disconnect()
            _G.WeaponConnection = nil
        end
        
        if _G.WeaponEnabled then
            _G.WeaponConnection = Workspace.ChildAdded:Connect(function(child)
                pcall(function()
                    if not _G.WeaponEnabled then return end
                    
                    -- Cek apakah player sedang memegang senjata saat peluru ditembakkan
                    if isHoldingGun() then
                        if child:IsA("BasePart") then
                            local char = LocalPlayer.Character
                            if char and not child:IsDescendantOf(char) then
                                task.wait(0.01)
                                
                                local targetPlayer = Players:FindFirstChild(_G.LockedTargetName)
                                if targetPlayer and targetPlayer.Character then
                                    local rootPart = targetPlayer.Character:FindFirstChild("HumanoidRootPart")
                                    local humanoid = targetPlayer.Character:FindFirstChildOfClass("Humanoid")
                                    
                                    if rootPart and humanoid and humanoid.Health > 0 then
                                        -- Teleport peluru ke target (Fungsidihapus bersih)
                                        child.CFrame = rootPart.CFrame
                                    end
                                end
                            end
                        end
                    end
                end)
            end)
        end
    end,
})

-- Contoh cara pakai Section di WindUI
Tabs.hacker:Section({ Title = "Auto Glitch NPC" })

-- 1. Slider untuk mengatur jarak radius glitch (bisa diatur sampai 100 meter atau lebih)
Tabs.hacker:Slider({
    Title = "Glitch",
    Description = "Atur jarak radius NPC agar otomatis glitch dan tiduran saat mendekat",
    Value = {
        Min = 5,
        Max = 500, -- Bisa diatur sampai 100 meter atau lebih
        Default = 30,
    },
    Callback = function(Value)
        GlitchLayRadius = Value
    end,
})

-- 2. Toggle untuk mengaktifkan/menonaktifkan Auto Glitch & Lay Down
Tabs.hacker:Toggle({
    Title = "Auto Glitch NPC",
    Description = "NPC yang mendekati kamu otomatis glitch dan tiduran kaku!",
    Value = false,
    Callback = function(Value)
        AutoGlitchLayEnabled = Value
    end,
})

-- 3. Loop latar belakang yang aman, cepat, dan anti-lag
task.spawn(function()
    while true do
        task.wait(0.1) -- Cek berkala agar tetap mulus
        
        if AutoGlitchLayEnabled then
            local Character = LocalPlayer.Character
            if Character then
                local RootPart = Character:FindFirstChild("HumanoidRootPart")
                if RootPart then
                    local playerList = Players:GetPlayers()
                    
                    for _, obj in ipairs(workspace:GetDescendants()) do
                        if obj:IsA("Model") and obj ~= Character then
                            local humanoid = obj:FindFirstChildOfClass("Humanoid")
                            if humanoid and humanoid.Health > 0 then
                                local npcRoot = obj:FindFirstChild("HumanoidRootPart") or obj:FindFirstChild("PrimaryPart")
                                if npcRoot then
                                    -- Pastikan bukan player lain
                                    local isPlayer = false
                                    for _, p in ipairs(playerList) do
                                        if p.Character == obj then
                                            isPlayer = true
                                            break
                                        end
                                    end
                                    
                                    if not isPlayer then
                                        local distance = (RootPart.Position - npcRoot.Position).Magnitude
                                        if distance <= GlitchLayRadius then
                                            pcall(function()
                                                -- Lumpuhkan gerakan NPC
                                                humanoid.PlatformStand = true
                                                humanoid.WalkSpeed = 0
                                                humanoid.JumpPower = 0
                                                humanoid.AutoRotate = false
                                                
                                                -- Ubah state humanoid jadi Physics / FallingDown biar kelihatan tiduran/guling-guling
                                                pcall(function()
                                                    humanoid:ChangeState(Enum.HumanoidStateType.Physics)
                                                end)
                                                
                                                -- Berikan efek rotasi glitch berputar-putar dan goyang-goyang lucu
                                                if npcRoot:IsA("BasePart") then
                                                    npcRoot.AssemblyAngularVelocity = Vector3.new(
                                                        math.random(-50, 50),
                                                        math.random(-100, 100),
                                                        math.random(-50, 50)
                                                    )
                                                end
                                            end)
                                        end
                                    end
                                end
                            end
                        end
                    end
                end
            end
        end
    end
end)

--==================================================
-- VISUALS
--==================================================

-- Fullbright
local FullbrightConnection

local OldBrightness
local OldClockTime
local OldFogEnd
local OldGlobalShadows
local OldOutdoorAmbient

Tabs.Visuals:Toggle({
    Title = "Fullbright",
    Description = "Menerangi lingkungan game",
    Value = false,

    Callback = function(Value)

        -- Matikan connection lama
        if FullbrightConnection then
            FullbrightConnection:Disconnect()
            FullbrightConnection = nil
        end

        if Value then
            -- Simpan setting asli SATU KALI
            OldBrightness = Lighting.Brightness
            OldClockTime = Lighting.ClockTime
            OldFogEnd = Lighting.FogEnd
            OldGlobalShadows = Lighting.GlobalShadows
            OldOutdoorAmbient = Lighting.OutdoorAmbient

            local function ApplyFullbright()
                Lighting.Brightness = 2
                Lighting.ClockTime = 14
                Lighting.FogEnd = 100000
                Lighting.GlobalShadows = false
                Lighting.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
            end

            ApplyFullbright()

            -- Pertahankan Fullbright selama toggle ON
            FullbrightConnection = Lighting.Changed:Connect(function()
                ApplyFullbright()
            end)

        else
            -- Kembalikan setting Lighting seperti sebelum Fullbright
            if OldBrightness ~= nil then
                Lighting.Brightness = OldBrightness
            end

            if OldClockTime ~= nil then
                Lighting.ClockTime = OldClockTime
            end

            if OldFogEnd ~= nil then
                Lighting.FogEnd = OldFogEnd
            end

            if OldGlobalShadows ~= nil then
                Lighting.GlobalShadows = OldGlobalShadows
            end

            if OldOutdoorAmbient ~= nil then
                Lighting.OutdoorAmbient = OldOutdoorAmbient
            end
        end
    end,
})

--==================================================
-- SETTINGS
--==================================================

Tabs.Settings:Button({
    Title = "Destroy GUI",
    Description = "Menutup interface WindUI",

    Callback = function()
        if Window.Destroy then
            Window:Destroy()
        end
    end,
})

Tabs.Settings:Button({
    Title = "Reset",
    Description = "Mereset karakter",

    Callback = function()
        local Character = LocalPlayer.Character
        local Humanoid = Character and Character:FindFirstChildOfClass("Humanoid")

        if Humanoid then
            Humanoid.Health = 0
        end
    end,
})

Tabs.Settings:Button({
    Title = "Rejoin Server",
    Description = "Masuk kembali ke server saat ini",

    Callback = function()
        TeleportService:Teleport(
            game.PlaceId,
            LocalPlayer
        )
    end,
})

Tabs.Settings:Button({
    Title = "Server Hop",
    Description = "Otomatis memindahkanmu ke server yang paling sepi pemain",
    Callback = function()
        local TeleportService = game:GetService("TeleportService")
        local HttpService = game:GetService("HttpService")
        local Players = game:GetService("Players")
        
        WindUI:Notify({
            Title = "Server Hop",
            Content = "Mencari server sepi, mohon tunggu...",
            Duration = 3,
        })
        
        -- Ambil daftar server dari API Roblox
        local success, servers = pcall(function()
            local url = "https://games.roblox.com/v1/games/" .. game.PlaceId .. "/servers/Public?sortOrder=Asc&limit=100"
            return HttpService:JSONDecode(game:HttpGet(url))
        end)
        
        if success and servers and servers.data then
            local targetServerId = nil
            
            -- Cari server yang jumlah playernya paling sedikit (dari urutan bawah)
            for _, server in ipairs(servers.data) do
                if type(server) == "table" and server.playing and server.maxPlayers and server.id then
                    -- Pastikan bukan server tempat kamu berada sekarang dan player kurang dari max
                    if server.playing < (server.maxPlayers - 1) and server.id ~= game.JobId then
                        targetServerId = server.id
                        break
                    end
                end
            end
            
            if targetServerId then
                WindUI:Notify({
                    Title = "Server Hop",
                    Content = "Server Hop...",
                    Duration = 3,
                })
                TeleportService:TeleportToPlaceInstance(game.PlaceId, targetServerId, Players.LocalPlayer)
            else
                WindUI:Notify({
                    Title = "Server Hop",
                    Content = "server Hop",
                    Duration = 3,
                })
            end
        else
            WindUI:Notify({
                Title = "Server Hop",
                Content = "server Hop",
                Duration = 3,
            })
        end
    end,
})

--==================================================
-- NOTIFICATION
--==================================================

WindUI:Notify({
    Title = "scriptDEX1",
    Content = "Universal Hub berhasil dimuat!",
    Duration = 4,
})
