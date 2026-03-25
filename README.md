-- EVITA DUPLICAR
if _G.EmoteGUI then return end
_G.EmoteGUI = true

-- // Configurações //
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

-- GUI
local screenGui = Instance.new("ScreenGui", game.CoreGui)
screenGui.Name = "EmoteGUI"
screenGui.Enabled = false

_G.EmoteToggle = function(state)
    screenGui.Enabled = state
end

local frame = Instance.new("Frame", screenGui)
frame.Size = UDim2.new(0, 250, 0, 120)
frame.Position = UDim2.new(0.5, -125, 0.5, -60)
frame.BackgroundColor3 = Color3.fromRGB(25,25,25)
frame.Active = true
frame.Draggable = true
Instance.new("UICorner", frame)

local textLabel = Instance.new("TextLabel", frame)
textLabel.Size = UDim2.new(0.6,0,0.3,0)
textLabel.Position = UDim2.new(0.2,0,0.1,0)
textLabel.TextScaled = true
textLabel.BackgroundTransparency = 1
textLabel.TextColor3 = Color3.new(1,1,1)

local prevBtn = Instance.new("TextButton", frame)
prevBtn.Size = UDim2.new(0.2,0,0.3,0)
prevBtn.Position = UDim2.new(0,0,0.1,0)
prevBtn.Text = "<"

local nextBtn = Instance.new("TextButton", frame)
nextBtn.Size = UDim2.new(0.2,0,0.3,0)
nextBtn.Position = UDim2.new(0.8,0,0.1,0)
nextBtn.Text = ">"

local playBtn = Instance.new("TextButton", frame)
playBtn.Size = UDim2.new(1,0,0.4,0)
playBtn.Position = UDim2.new(0,0,0.6,0)
playBtn.Text = "Play"

-- SOM
local function SOUND(id)
    local sound = Instance.new("Sound")
    sound.SoundId = "rbxassetid://"..id
    sound.Parent = char:WaitForChild("HumanoidRootPart")
    sound:Play()
    game.Debris:AddItem(sound,5)
end

-- EMOTE
local function playEmote()
    local emote = emotes[emoteNames[currentIndex]]
    if not emote then return end

    SOUND(emote[1])

    local anim = Instance.new("Animation")
    anim.AnimationId = "rbxassetid://"..emote[2]
    local track = humanoid:LoadAnimation(anim)
    track:Play()
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
