local Player = game.Players.LocalPlayer
local RunService = game:GetService("RunService")

local targetSpeed = 50
local isEnabled = false
local currentBaseSpeed = 16

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "FinalSpeedBypass"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = Player:WaitForChild("PlayerGui")

local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Size = UDim2.new(0, 180, 0, 110)
MainFrame.Position = UDim2.new(0.5, -90, 0.7, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MainFrame.Active = true
MainFrame.Draggable = true
Instance.new("UICorner", MainFrame)

local MiniFrame = Instance.new("TextButton", ScreenGui)
MiniFrame.Size = UDim2.new(0, 30, 0, 30)
MiniFrame.Visible = false
MiniFrame.Text = "+"
MiniFrame.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
MiniFrame.TextColor3 = Color3.fromRGB(255, 255, 255)
MiniFrame.Draggable = true
Instance.new("UICorner", MiniFrame)

local Input = Instance.new("TextBox", MainFrame)
Input.Size = UDim2.new(0, 140, 0, 30)
Input.Position = UDim2.new(0.5, -70, 0, 35)
Input.Text = "50"
Input.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
Input.TextColor3 = Color3.fromRGB(255, 255, 255)
Instance.new("UICorner", Input)

local ToggleBtn = Instance.new("TextButton", MainFrame)
ToggleBtn.Size = UDim2.new(0, 140, 0, 30)
ToggleBtn.Position = UDim2.new(0.5, -70, 0, 70)
ToggleBtn.Text = "OFF"
ToggleBtn.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
ToggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
Instance.new("UICorner", ToggleBtn)

local MinimizeBtn = Instance.new("TextButton", MainFrame)
MinimizeBtn.Size = UDim2.new(0, 20, 0, 20)
MinimizeBtn.Position = UDim2.new(1, -25, 0, 5)
MinimizeBtn.Text = "–"
MinimizeBtn.BackgroundTransparency = 1
MinimizeBtn.TextColor3 = Color3.fromRGB(255, 255, 255)

local bV = Instance.new("BodyVelocity")
bV.MaxForce = Vector3.new(100000, 0, 100000)
bV.P = 1250

MinimizeBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = false
    MiniFrame.Position = MainFrame.Position
    MiniFrame.Visible = true
end)

MiniFrame.MouseButton1Click:Connect(function()
    MiniFrame.Visible = false
    MainFrame.Position = MiniFrame.Position
    MainFrame.Visible = true
end)

Input.FocusLost:Connect(function()
    targetSpeed = tonumber(Input.Text) or 16
end)

ToggleBtn.MouseButton1Click:Connect(function()
    isEnabled = not isEnabled
    
    if isEnabled and Player.Character and Player.Character:FindFirstChild("Humanoid") then
        currentBaseSpeed = Player.Character.Humanoid.WalkSpeed
    end
    
    ToggleBtn.Text = isEnabled and "ON" or "OFF"
    ToggleBtn.BackgroundColor3 = isEnabled and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(150, 0, 0)
    MiniFrame.BackgroundColor3 = ToggleBtn.BackgroundColor3
end)

RunService.RenderStepped:Connect(function()
    local char = Player.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    local hum = char and char:FindFirstChild("Humanoid")

    if isEnabled and root and hum then
        hum:SetStateEnabled(Enum.HumanoidStateType.FallingDown, false)
        hum:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, false)
        hum.WalkSpeed = currentBaseSpeed
        
        if hum.MoveDirection.Magnitude > 0 then
            bV.Parent = root
            bV.Velocity = hum.MoveDirection * targetSpeed
        else
            bV.Parent = nil
        end
    else
        bV.Parent = nil
        if hum then
            hum:SetStateEnabled(Enum.HumanoidStateType.FallingDown, true)
            hum:SetStateEnabled(Enum.HumanoidStateType.Ragdoll, true)
        end
    end
end)