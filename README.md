-- Aimbot com Auto-Shoot para ACS

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local Camera = workspace.CurrentCamera

local FOV_RADIUS = 100
local TARGET_PART = "Head"
local SHOOT_INTERVAL = 0.15
local lastShot = 0

-- Função para encontrar o inimigo mais próximo dentro do FOV
local function getClosestTarget()
    local closest = nil
    local shortestDistance = FOV_RADIUS
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild(TARGET_PART) then
            local part = player.Character:FindFirstChild(TARGET_PART)
            local pos, onScreen = Camera:WorldToViewportPoint(part.Position)
            if onScreen then
                local dist = (Vector2.new(pos.X, pos.Y) - Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)).Magnitude
                if dist < shortestDistance then
                    shortestDistance = dist
                    closest = part
                end
            end
        end
    end
    return closest
end

-- Loop principal
RunService.RenderStepped:Connect(function()
    local target = getClosestTarget()
    if target then
        -- Mira no alvo
        Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Position)

        -- Auto-Shoot
        if tick() - lastShot >= SHOOT_INTERVAL then
            local tool = LocalPlayer.Character:FindFirstChildOfClass("Tool")
            if tool then
                local fireEvent = tool:FindFirstChild("Fire")
                if fireEvent then
                    fireEvent:FireServer()
                    lastShot = tick()
                end
            end
        end
    end
end)
