local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local SocialService = game:GetService("SocialService")

local function FindBall()
    for _, ball in pairs(workspace.Balls:GetChildren()) do
        if ball:GetAttribute("realBall") then
            return true
        end
    end
    return false
end

local function Parry(remote, entityAim, direction)
    if not entityAim or not direction then
        warn("Cannot parry: Invalid entity or direction")
        return
    end

    local targetPosition = workspace.CurrentCamera:WorldToScreenPoint(entityAim.Position)
    local finalDirection

    if direction == "High" then
        finalDirection = CFrame.new(workspace.CurrentCamera.CFrame.Position, Vector3.new(0, 1000, 0))
    elseif direction == "Random" then
        finalDirection = CFrame.new(
            workspace.CurrentCamera.CFrame.Position,
            Vector3.new(math.random(-1000, 1000), math.random(-100, 1000), math.random(-1000, 1000))
        )
    else
        finalDirection = CFrame.new() -- Default direction or custom logic
    end

    remote:FireServer(
        0.5,
        finalDirection,
        {[entityAim.Name] = targetPosition},
        {targetPosition.X, targetPosition.Y},
        false
    )
end

local function IsTarget()
    return Player.Character and Player.Character:FindFirstChild("Highlight")
end

local ParryService = {
    entityAim = nil,
    direction = 'High', -- Default direction
    remote = SocialService.RemoteEvent,
    lastParryTime = 0,
    parryCooldown = 2, -- Cooldown duration in seconds
    comboCount = 0,
    maxComboCount = 3, -- Maximum number of combo parries
    comboWindow = 0.5, -- Time window for combo parries in seconds

    canParry: function(self)
        local currentTime = tick()
        return (currentTime - self.lastParryTime >= self.parryCooldown)
    end,

    parry: function(self)
        if not self.entityAim then
            warn("Cannot parry: entityAim is nil")
            return
        end

        if not self:canParry() then
            warn("Cannot parry: Cooldown in effect")
            return
        end

        -- Timed parry logic
        if self.comboCount > 0 and tick() - self.lastParryTime <= self.comboWindow then
            -- Successful combo parry
            self.comboCount = self.comboCount - 1
            print("Combo Parry! Remaining: " .. self.comboCount)
        else
            self.comboCount = self.maxComboCount - 1
            print("First Parry in Combo!")
        end

        Parry(self.remote, self.entityAim, self.direction)
        self.lastParryTime = tick() -- Update last parry time
    end,

    dungeonParry: function(self)
        self.remote:FireServer(
            workspace.CurrentCamera.CFrame,
            workspace.CurrentCamera.CFrame,
            false
        )
    end,

    parryWithFeedback: function(self)
        if not self.entityAim then
            warn("Cannot parry: entityAim is nil")
            return
        end

        if not self:canParry() then
            warn("Cannot parry: Cooldown in effect")
            return
        end

        if not IsInRange(self.entityAim, 20) then
            warn("Cannot parry: Target is out of range")
            return
        end

        -- Additional visual feedback logic here

        self:parry() -- Perform the parry action
    end
}

return ParryService
