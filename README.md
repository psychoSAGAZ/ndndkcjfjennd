-- // Configurações dos Emotes // --
local emotes = {
    Laugh = {154157543, 12521018724},
    Hi = {154147007, 12521004586},
    Bye = {154157386, 12521004586},
    Sleep = {868409374, 2695918332},
}

local emoteNames = {}
for name in pairs(emotes) do
    table.insert(emoteNames, name)
end

local currentIndex = 1
local player = game.Players.LocalPlayer
local char, humanoid

local function updateCharacter()
    char = player.Character or player.CharacterAdded:Wait()
    humanoid = char:WaitForChild("Humanoid")
end

player.CharacterAdded:Connect(updateCharacter)
updateCharacter()

-- // Criação da GUI Original // --
local screenGui = Instance.new("ScreenGui", game:GetService("CoreGui"))
screenGui.Enabled = false
_G.EmoteGui = screenGui -- Única alteração: necessária para o loadstring funcionar com o toggle

local camera = workspace.CurrentCamera
local viewportSize = camera.ViewportSize
local frameWidth = viewportSize.X * 0.15
local frameHeight = viewportSize.Y * 0.10

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, frameWidth, 0, frameHeight)
frame.Position = UDim2.new(0.5, -frameWidth / 2, 0.5, -frameHeight / 2)
frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
frame.BackgroundTransparency = 0.3
frame.BorderSizePixel = 0
frame.Parent = screenGui
frame.Draggable = true
frame.Active = true

Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 10)

local textLabel = Instance.new("TextLabel", frame)
textLabel.Size = UDim2.new(0.6, 0, 0.35, 0)
textLabel.Position = UDim2.new(0.2, 0, 0.15, 0)
textLabel.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
textLabel.BackgroundTransparency = 0.2
textLabel.TextScaled = true
textLabel.TextColor3 = Color3.new(1, 1, 1)
textLabel.Font = Enum.Font.GothamBold
Instance.new("UICorner", textLabel).CornerRadius = UDim.new(0, 6)

local prevBtn = Instance.new("TextButton", frame)
prevBtn.Size = UDim2.new(0.15, 0, 0.35, 0)
prevBtn.Position = UDim2.new(0.025, 0, 0.15, 0)
prevBtn.Text = "<"
prevBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
prevBtn.BackgroundTransparency = 0.2
prevBtn.TextScaled = true
prevBtn.TextColor3 = Color3.new(1, 1, 1)
prevBtn.Font = Enum.Font.GothamBold
Instance.new("UICorner", prevBtn).CornerRadius = UDim.new(0, 6)

local nextBtn = Instance.new("TextButton", frame)
nextBtn.Size = UDim2.new(0.15, 0, 0.35, 0)
nextBtn.Position = UDim2.new(0.825, 0, 0.15, 0)
nextBtn.Text = ">"
nextBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
nextBtn.BackgroundTransparency = 0.2
nextBtn.TextScaled = true
nextBtn.TextColor3 = Color3.new(1, 1, 1)
nextBtn.Font = Enum.Font.GothamBold
Instance.new("UICorner", nextBtn).CornerRadius = UDim.new(0, 6)

local playBtn = Instance.new("TextButton", frame)
playBtn.Size = UDim2.new(0.95, 0, 0.3, 0)
playBtn.Position = UDim2.new(0.025, 0, 0.6, 0)
playBtn.Text = "Play"
playBtn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
playBtn.BackgroundTransparency = 0.1
playBtn.TextScaled = true
playBtn.TextColor3 = Color3.new(1, 1, 1)
playBtn.Font = Enum.Font.GothamMedium
Instance.new("UICorner", playBtn).CornerRadius = UDim.new(0, 8)

-- // FUNÇÃO SOUND ATUALIZADA 🔥 // --
local function SOUND(id)
    local Players = game:GetService("Players")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")

    for _, plr in ipairs(Players:GetPlayers()) do
        if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
            local hrp = plr.Character.HumanoidRootPart

            local args = {
                [1] = hrp,
                [2] = hrp,
                [3] = Vector3.new(0, -1, 0),
                [4] = Vector3.new(0, 0, 0),
                [5] = hrp,
                [6] = hrp,
                [7] = tostring(id),
                [8] = tostring(id),
                [9] = { [1] = true },
                [10] = {
                    [1] = 25,
                    [2] = Vector3.new(0.25, 0.25, 5),
                    [3] = BrickColor.new(24),
                    [4] = 0.25,
                    [5] = Enum.Material.SmoothPlastic,
                    [6] = 0.25
                },
                [11] = true,
                [12] = false
            }

            local remote = ReplicatedStorage:FindFirstChild("RE")
            if remote and remote:FindFirstChild("1Gu1n") then
                pcall(function()
                    remote["1Gu1n"]:FireServer(unpack(args))
                end)
            end
        end
    end

    local sound = Instance.new("Sound")
    sound.SoundId = "rbxassetid://" .. id
    sound.Volume = 1
    sound.Parent = char:WaitForChild("HumanoidRootPart")
    sound:Play()
    sound.Ended:Connect(function()
        sound:Destroy()
    end)
end

-- // RESTO IGUAL // --
local function playEmote()
    if not char or not humanoid then return end
    local emote = emotes[emoteNames[currentIndex]]
    if not emote then return end

    SOUND(emote[1])

    local originalSpeed = humanoid.WalkSpeed
    local originalJump = humanoid.JumpPower

    humanoid.WalkSpeed = 0
    humanoid.JumpPower = 0
    task.wait(0.1)

    for _, track in pairs(humanoid:GetPlayingAnimationTracks()) do
        track:Stop()
    end

    local anim = Instance.new("Animation")
    anim.AnimationId = "rbxassetid://" .. emote[2]
    local track = humanoid:LoadAnimation(anim)
    track.Looped = false
    track:Play()

    track.Stopped:Connect(function()
        humanoid.WalkSpeed = originalSpeed
        humanoid.JumpPower = originalJump
        anim:Destroy()
    end)
end

local function updateUI()
    textLabel.Text = emoteNames[currentIndex]
end

prevBtn.MouseButton1Click:Connect(function()
    currentIndex = (currentIndex - 2) % #emoteNames + 1
    updateUI()
end)

nextBtn.MouseButton1Click:Connect(function()
    currentIndex = (currentIndex % #emoteNames) + 1
    updateUI()
end)

playBtn.MouseButton1Click:Connect(playEmote)
updateUI()
