# Robloxscripts
[🎮]([url](https://www.roblox.com/games/14113053169/Free-Client-Script-Executor))




```
local player = game.Players.LocalPlayer
local mouse = player:GetMouse()

-- Create ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Create Main Frame for GUI
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 300, 0, 400)  -- Adjusted height for all the buttons
mainFrame.Position = UDim2.new(0, 0, 1, -400)  -- Positioned at the bottom-left of the screen
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.BackgroundTransparency = 0.2
mainFrame.BorderSizePixel = 0
mainFrame.AnchorPoint = Vector2.new(0, 1)  -- Anchor at the bottom-left corner
mainFrame.Parent = screenGui

-- Rounded Corners and Beveled Edges
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 12)
corner.Parent = mainFrame

local stroke = Instance.new("UIStroke")
stroke.Color = Color3.fromRGB(255, 255, 255)
stroke.Thickness = 2
stroke.Parent = mainFrame

-- Add UI drag detector to make the frame draggable
local dragDetector = Instance.new("UIDragDetector")
dragDetector.Parent = mainFrame

-- Create Button for Fly
local flyButton = Instance.new("TextButton")
flyButton.Size = UDim2.new(0, 120, 0, 40)
flyButton.Position = UDim2.new(0, 10, 0, 10)
flyButton.Text = "Fly"
flyButton.TextColor3 = Color3.fromRGB(255, 255, 255)
flyButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
flyButton.Font = Enum.Font.Gotham
flyButton.TextSize = 18
flyButton.Parent = mainFrame

-- Fly functionality
local flying = false
local humanoid = player.Character and player.Character:WaitForChild("Humanoid")
local bodyVelocity
local bodyGyro
local flySpeed = 50
local verticalSpeed = 0
local upDownSpeed = 20
local maxFlySpeed = 100  -- Maximum horizontal speed while flying

flyButton.MouseButton1Click:Connect(function()
    if not flying then
        -- Start flying
        flying = true
        bodyVelocity = Instance.new("BodyVelocity")
        bodyGyro = Instance.new("BodyGyro")
        bodyVelocity.MaxForce = Vector3.new(4000, 4000, 4000)
        bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        bodyGyro.MaxTorque = Vector3.new(4000, 4000, 4000)
        bodyGyro.CFrame = player.Character.HumanoidRootPart.CFrame
        bodyVelocity.Parent = player.Character.HumanoidRootPart
        bodyGyro.Parent = player.Character.HumanoidRootPart
        
        -- Set the humanoid state to flying (idle animation)
        humanoid:ChangeState(Enum.HumanoidStateType.Physics)
        humanoid.PlatformStand = true  -- Disables other actions while flying
    else
        -- Stop flying
        flying = false
        if bodyVelocity then
            bodyVelocity:Destroy()
        end
        if bodyGyro then
            bodyGyro:Destroy()
        end
        
        -- Set humanoid state back to idle
        humanoid:ChangeState(Enum.HumanoidStateType.GettingUp)
        humanoid.PlatformStand = false
    end
end)

-- Smooth Fly Movement with WASD & Space/Shift for vertical control
game:GetService("UserInputService").InputBegan:Connect(function(input, gameProcessed)
    if flying and not gameProcessed then
        local moveDirection = Vector3.new()
        
        -- Get the camera's look and right vectors to define movement directions
        local camera = game.Workspace.CurrentCamera
        local lookDirection = camera.CFrame.LookVector
        local rightDirection = camera.CFrame.RightVector

        -- WASD for horizontal movement, based on camera orientation
        if input.KeyCode == Enum.KeyCode.W then
            moveDirection = moveDirection + lookDirection
        elseif input.KeyCode == Enum.KeyCode.S then
            moveDirection = moveDirection - lookDirection
        elseif input.KeyCode == Enum.KeyCode.A then
            moveDirection = moveDirection - rightDirection
        elseif input.KeyCode == Enum.KeyCode.D then
            moveDirection = moveDirection + rightDirection
        end

        -- Space for upward movement
        if input.KeyCode == Enum.KeyCode.Space then
            verticalSpeed = upDownSpeed  -- Move up when Space is pressed
        elseif input.KeyCode == Enum.KeyCode.LeftShift then
            verticalSpeed = -upDownSpeed  -- Move down when Shift is pressed
        end

        -- Apply the velocity based on movement direction and speed
        bodyVelocity.Velocity = Vector3.new(moveDirection.X * flySpeed, verticalSpeed, moveDirection.Z * flySpeed)

        -- Rotate character to face the direction of movement based on camera
        player.Character.HumanoidRootPart.CFrame = CFrame.new(player.Character.HumanoidRootPart.Position, player.Character.HumanoidRootPart.Position + lookDirection)
    end
end)

-- Stop vertical movement when no keys are pressed
game:GetService("UserInputService").InputEnded:Connect(function(input)
    if flying then
        if input.KeyCode == Enum.KeyCode.Space or input.KeyCode == Enum.KeyCode.LeftShift then
            verticalSpeed = 0  -- Stop vertical movement when Space or Shift is released
        end
    end
end)

-- Create Button for Speed Boost
local speedButton = Instance.new("TextButton")
speedButton.Size = UDim2.new(0, 120, 0, 40)
speedButton.Position = UDim2.new(0, 10, 0, 60)
speedButton.Text = "Speed Boost"
speedButton.TextColor3 = Color3.fromRGB(255, 255, 255)
speedButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
speedButton.Font = Enum.Font.Gotham
speedButton.TextSize = 18
speedButton.Parent = mainFrame

-- Speed Boost functionality
local speedBoost = false
speedButton.MouseButton1Click:Connect(function()
    speedBoost = not speedBoost
    if speedBoost then
        humanoid.WalkSpeed = 100  -- Increase speed
    else
        humanoid.WalkSpeed = 16   -- Default speed
    end
end)

-- Create Button for Jump Boost
local jumpButton = Instance.new("TextButton")
jumpButton.Size = UDim2.new(0, 120, 0, 40)
jumpButton.Position = UDim2.new(0, 10, 0, 110)
jumpButton.Text = "Jump Boost"
jumpButton.TextColor3 = Color3.fromRGB(255, 255, 255)
jumpButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
jumpButton.Font = Enum.Font.Gotham
jumpButton.TextSize = 18
jumpButton.Parent = mainFrame

-- Jump Boost functionality
local jumpBoost = false
jumpButton.MouseButton1Click:Connect(function()
    jumpBoost = not jumpBoost
    if jumpBoost then
        humanoid.JumpPower = 150  -- Increase jump power
    else
        humanoid.JumpPower = 50   -- Default jump power
    end
end)

-- Create Button for Spin
local spinButton = Instance.new("TextButton")
spinButton.Size = UDim2.new(0, 120, 0, 40)
spinButton.Position = UDim2.new(0, 10, 0, 160)
spinButton.Text = "Spin"
spinButton.TextColor3 = Color3.fromRGB(255, 255, 255)
spinButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
spinButton.Font = Enum.Font.Gotham
spinButton.TextSize = 18
spinButton.Parent = mainFrame

-- Create Spin Speed TextBox
local spinTextBox = Instance.new("TextBox")
spinTextBox.Size = UDim2.new(0, 120, 0, 40)
spinTextBox.Position = UDim2.new(0, 10, 0, 210)
spinTextBox.PlaceholderText = "Spin Speed"
spinTextBox.TextColor3 = Color3.fromRGB(255, 255, 255)
spinTextBox.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
spinTextBox.Font = Enum.Font.Gotham
spinTextBox.TextSize = 18
spinTextBox.Parent = mainFrame

-- Spin functionality
local spinning = false
local spinSpeed = 100  -- Default spin speed

spinButton.MouseButton1Click:Connect(function()
    spinning = not spinning
    if spinning then
        -- Start spinning
        local spinTime = 0
        spinSpeed = tonumber(spinTextBox.Text) or 100  -- Get the spin speed from the TextBox
        while spinning do
            player.Character.HumanoidRootPart.CFrame = player.Character.HumanoidRootPart.CFrame * CFrame.Angles(0, math.rad(spinSpeed), 0)
            wait(0.03)  -- Update speed based on the spin speed (higher speed means faster updates)
        end
    end
end)

-- Create Button for ESP
local espButton = Instance.new("TextButton")
espButton.Size = UDim2.new(0, 120, 0, 40)
espButton.Position = UDim2.new(0, 10, 0, 260)
espButton.Text = "ESP"
espButton.TextColor3 = Color3.fromRGB(255, 255, 255)
espButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
espButton.Font = Enum.Font.Gotham
espButton.TextSize = 18
espButton.Parent = mainFrame

-- ESP functionality
local espEnabled = false
local highlights = {}

-- Function to toggle ESP
local function toggleESP()
    espEnabled = not espEnabled
    
    -- Clear existing highlights
    for _, highlight in pairs(highlights) do
        highlight:Destroy()
    end
    highlights = {}

    if espEnabled then
        -- Add highlight to all humanoids in the game
        for _, player in pairs(game.Players:GetPlayers()) do
            if player.Character and player.Character:FindFirstChild("Humanoid") then
                local humanoid = player.Character.Humanoid
                for _, part in pairs(player.Character:GetChildren()) do
                    if part:IsA("Part") and part.Parent == player.Character then
                        local highlight = Instance.new("Highlight")
                        highlight.Adornee = part
                        highlight.FillTransparency = 0.5
                        highlight.FillColor = Color3.fromRGB(255, 0, 0)  -- Red highlight
                        highlight.OutlineTransparency = 0.2
                        highlight.OutlineColor = Color3.fromRGB(255, 0, 0)
                        highlight.Parent = part
                        table.insert(highlights, highlight)
                    end
                end
            end
        end
    end
end

-- Button click event to toggle ESP
espButton.MouseButton1Click:Connect(function()
    toggleESP()
end)

-- Teleport to Mouse (when Ctrl is pressed and mouse clicked)
game:GetService("UserInputService").InputBegan:Connect(function(input, gameProcessed)
    if input.UserInputType == Enum.UserInputType.MouseButton1 and gameProcessed == false and game:GetService("UserInputService"):IsKeyDown(Enum.KeyCode.LeftControl) then
        local targetPosition = mouse.Hit.p
        player.Character:SetPrimaryPartCFrame(CFrame.new(targetPosition))
    end
end)

```
