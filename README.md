-- Client-Sided Skin Changer for Rivals roblox
local CoreGui = game:Service("CoreGui")
local Players = game:Service("Players")
local LocalPlayer = Players.LocalPlayer

-- Clean up older UI versions to allow script refreshing
if CoreGui:FindFirstChild("KoraxSkinHub") then
    CoreGui.KoraxSkinHub:Destroy()
end

-- 1. Premium Dark Theme Dashboard Wrapper
local UI = Instance.new("ScreenGui")
UI.Name = "KoraxSkinHub"
UI.Parent = CoreGui

local Window = Instance.new("Frame")
Window.Size = UDim2.new(0, 220, 0, 300)
Window.Position = UDim2.new(0.05, 0, 0.25, 0)
Window.BackgroundColor3 = Color3.fromRGB(15, 15, 18) -- Deep Korax Dark
Window.BorderSizePixel = 0
Window.Parent = UI

-- Rounded Corners for UI
local Corner = Instance.new("UICorner")
Corner.CornerRadius = UDim.new(0, 8)
Corner.Parent = Window

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 45)
Title.BackgroundColor3 = Color3.fromRGB(24, 24, 30)
Title.Text = "KORAX // SKINS"
Title.TextColor3 = Color3.fromRGB(0, 255, 200) -- Neon Cyan Theme Accent
Title.Font = Enum.Font.RobotoMono
Title.TextSize = 16
Title.Parent = Window

local TitleCorner = Instance.new("UICorner")
TitleCorner.CornerRadius = UDim.new(0, 8)
TitleCorner.Parent = Title

-- 2. Skin Target ID Map Database 
-- Replace placeholder rbxassetids with actual active Roblox catalog asset values
local Skins = {
    ["Freeze Rifle"]  = { Asset = "rbxassetid://10842880053", Shade = Color3.fromRGB(0, 240, 255) },
    ["Molten Sniper"] = { Asset = "rbxassetid://10842883002", Shade = Color3.fromRGB(255, 60, 0) },
    ["Void Shotgun"]  = { Asset = "rbxassetid://10842885009", Shade = Color3.fromRGB(130, 0, 255) },
    ["Gold Pistol"]   = { Asset = "rbxassetid://10842889001", Shade = Color3.fromRGB(255, 200, 0) }
}

-- 3. Core Engine: Recursive Local Scan Loop
local function EquipSkin(targetWeapon)
    local char = LocalPlayer.Character
    if not char then return end
    
    -- Recursively search the player object tree for gun objects
    for _, item in pairs(char:GetDescendants()) do
        if item:IsA("Tool") or item:IsA("Model") then
            -- Match name strings locally
            if string.find(string.lower(item.Name), string.lower(targetWeapon)) or string.find(string.lower(targetWeapon), string.lower(item.Name)) then
                -- Target mesh instances within weapon models
                for _, part in pairs(item:GetDescendants()) do
                    if part:IsA("MeshPart") or part:IsA("SpecialMesh") then
                        local configuration = Skins[targetWeapon]
                        if configuration then
                            -- Local client rendering override
                            part.TextureID = configuration.Asset
                            part.Color = configuration.Shade
                        end
                    end
                end
            end
        end
    end
end

-- 4. Dynamic Menu Button System Generator
local yPos = 55
for weapon, _ in pairs(Skins) do
    local SkinBtn = Instance.new("TextButton")
    SkinBtn.Size = UDim2.new(0, 200, 0, 40)
    SkinBtn.Position = UDim2.new(0, 10, 0, yPos)
    SkinBtn.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
    SkinBtn.TextColor3 = Color3.fromRGB(230, 230, 235)
    SkinBtn.Text = "Apply " .. weapon
    SkinBtn.Font = Enum.Font.SourceSans
    SkinBtn.TextSize = 14
    SkinBtn.BorderSizePixel = 0
    SkinBtn.Parent = Window
    
    local BtnCorner = Instance.new("UICorner")
    BtnCorner.CornerRadius = UDim.new(0, 4)
    BtnCorner.Parent = SkinBtn
    
    -- Event Hook
    SkinBtn.MouseButton1Click:Connect(function()
        EquipSkin(weapon)
        SkinBtn.TextColor3 = Color3.fromRGB(0, 255, 200)
        SkinBtn.Text = weapon .. " Active"
        task.wait(1.5)
        SkinBtn.TextColor3 = Color3.fromRGB(230, 230, 235)
        SkinBtn.Text = "Apply " .. weapon
    end)
    
    yPos = yPos + 48
end
