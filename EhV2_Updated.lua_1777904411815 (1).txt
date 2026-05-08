
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/sid77ds7sududu/Xeioa/refs/heads/main/Test.lua"))()

local UI_READY = false
local LoadingConfig = false

local Window = Library:MakeWindow({
    Name = "Emergency Hamburg | Xeioa Hub V2",
    ConfigFolder = "EmergencyHamburgXeioaV2",
    SaveConfig = true,
    HidePremium = false,
    IntroEnabled = true,
    IntroText = "Xeioa",
    IntroIcon = "rbxassetid://134853151765745",
    IntroToggleIcon = "rbxassetid://134853151765745",
    ShowIcon = true,
    Icon = "rbxassetid://134853151765745",
    CloseCallback = function() end
})

Library:Init()

local Players            = game:GetService("Players")
local RunService         = game:GetService("RunService")
local UserInputService   = game:GetService("UserInputService")
local Workspace          = game:GetService("Workspace")
local ReplicatedStorage  = game:GetService("ReplicatedStorage")
local TweenService       = game:GetService("TweenService")
local Lighting           = game:GetService("Lighting")

local LP     = Players.LocalPlayer
local Camera = Workspace.CurrentCamera

local function notify(title, content, duration)
    Library:MakeNotification({
        Name = title or "Xeioa",
        Content = content or "",
        Image = "rbxassetid://4384403532",
        Time = duration or 3
    })
end

local MobileUI = Instance.new("ScreenGui")
MobileUI.Name = "EHX_MobileControls"
if not pcall(function() MobileUI.Parent = game:GetService("CoreGui") end) then
    MobileUI.Parent = LP:WaitForChild("PlayerGui")
end
MobileUI.Enabled = false

local MobileFrame = Instance.new("Frame")
MobileFrame.Parent = MobileUI
MobileFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
MobileFrame.BackgroundTransparency = 0.5
MobileFrame.Position = UDim2.new(0.8, 0, 0.5, 0)
MobileFrame.Size = UDim2.new(0, 140, 0, 140)
Instance.new("UICorner", MobileFrame).CornerRadius = UDim.new(0, 10)

local function makeDraggable(frame)
    local dragging, dragInput, dragStart, startPos
    frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = frame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then dragging = false end
            end)
        end
    end)
    frame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end
makeDraggable(MobileFrame)

local function createBtn(parent, text, pos, size, callbackDown, callbackUp)
    local btn = Instance.new("TextButton")
    btn.Parent = parent
    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    btn.BackgroundTransparency = 0.3
    btn.Position = pos
    btn.Size = size or UDim2.new(0, 60, 0, 60)
    btn.Text = text
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 18
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
    btn.MouseButton1Down:Connect(callbackDown)
    btn.MouseButton1Up:Connect(callbackUp)
    btn.MouseLeave:Connect(callbackUp)
    return btn
end

local mobileUp, mobileDown, mobileFwd, mobileBwd = false, false, false, false
createBtn(MobileFrame, "Fwd", UDim2.new(0.05, 0, 0.05, 0), nil, function() mobileFwd = true end, function() mobileFwd = false end)
createBtn(MobileFrame, "Up",  UDim2.new(0.55, 0, 0.05, 0), nil, function() mobileUp  = true end, function() mobileUp  = false end)
createBtn(MobileFrame, "Bwd", UDim2.new(0.05, 0, 0.55, 0), nil, function() mobileBwd = true end, function() mobileBwd = false end)
createBtn(MobileFrame, "Dn",  UDim2.new(0.55, 0, 0.55, 0), nil, function() mobileDown= true end, function() mobileDown= false end)

local CFrameFlyMobileUI = Instance.new("ScreenGui")
CFrameFlyMobileUI.Name = "EHX_CFrameFlyControls"
if not pcall(function() CFrameFlyMobileUI.Parent = game:GetService("CoreGui") end) then
    CFrameFlyMobileUI.Parent = LP:WaitForChild("PlayerGui")
end
CFrameFlyMobileUI.Enabled = false

local CFrameFlyFrame = Instance.new("Frame")
CFrameFlyFrame.Parent = CFrameFlyMobileUI
CFrameFlyFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
CFrameFlyFrame.BackgroundTransparency = 0.5
CFrameFlyFrame.Position = UDim2.new(0.02, 0, 0.5, 0)
CFrameFlyFrame.Size = UDim2.new(0, 220, 0, 160)
Instance.new("UICorner", CFrameFlyFrame).CornerRadius = UDim.new(0, 10)
makeDraggable(CFrameFlyFrame)

local cfMobileW, cfMobileS, cfMobileA, cfMobileD = false, false, false, false
local cfMobileUp, cfMobileDown = false, false

local function createCFBtn(text, pos, callbackDown, callbackUp)
    return createBtn(CFrameFlyFrame, text, pos, UDim2.new(0, 60, 0, 44), callbackDown, callbackUp)
end
createCFBtn("W",  UDim2.new(0,  80, 0,  5), function() cfMobileW    = true end, function() cfMobileW    = false end)
createCFBtn("S",  UDim2.new(0,  80, 0, 55), function() cfMobileS    = true end, function() cfMobileS    = false end)
createCFBtn("A",  UDim2.new(0,  15, 0, 55), function() cfMobileA    = true end, function() cfMobileA    = false end)
createCFBtn("D",  UDim2.new(0, 145, 0, 55), function() cfMobileD    = true end, function() cfMobileD    = false end)
createCFBtn("Up", UDim2.new(0,  15, 0,  5), function() cfMobileUp   = true end, function() cfMobileUp   = false end)
createCFBtn("Dn", UDim2.new(0, 145, 0,  5), function() cfMobileDown = true end, function() cfMobileDown = false end)

local AimMobileUI = Instance.new("ScreenGui")
AimMobileUI.Name = "EHX_AimMobile"
if not pcall(function() AimMobileUI.Parent = game:GetService("CoreGui") end) then
    AimMobileUI.Parent = LP:WaitForChild("PlayerGui")
end
AimMobileUI.Enabled = false

local AimMobileFrame = Instance.new("Frame")
AimMobileFrame.Parent = AimMobileUI
AimMobileFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
AimMobileFrame.BackgroundTransparency = 0.3
AimMobileFrame.Position = UDim2.new(0.5, 0, 0.82, 0)
AimMobileFrame.Size = UDim2.new(0, 115, 0, 98)
Instance.new("UICorner", AimMobileFrame).CornerRadius = UDim.new(0, 10)
makeDraggable(AimMobileFrame)

local mobileAimActive = false
local mobileAimMode   = "Hold"
local mobileAimToggleState = false
local mobileAimStyle  = "Normal"
local stickyTarget    = nil

local AimBtn = Instance.new("TextButton")
AimBtn.Parent = AimMobileFrame
AimBtn.BackgroundColor3 = Color3.fromRGB(160, 30, 30)
AimBtn.Position = UDim2.new(0, 5, 0, 5)
AimBtn.Size     = UDim2.new(1, -10, 1, -10)
AimBtn.Text     = "AIM"
AimBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
AimBtn.Font     = Enum.Font.SourceSansBold
AimBtn.TextSize = 22
Instance.new("UICorner", AimBtn).CornerRadius = UDim.new(0, 8)

local function updateAimBtnVisual()
    if mobileAimActive then
        AimBtn.BackgroundColor3 = Color3.fromRGB(255, 70, 70)
        AimBtn.Text = "AIMING"
    else
        AimBtn.BackgroundColor3 = Color3.fromRGB(160, 30, 30)
        AimBtn.Text = "AIM"
    end
end

AimBtn.MouseButton1Down:Connect(function()
    if mobileAimMode == "Hold" then
        mobileAimActive = true; updateAimBtnVisual()
    end
end)
AimBtn.MouseButton1Up:Connect(function()
    if mobileAimMode == "Hold" then
        mobileAimActive = false; updateAimBtnVisual()
    elseif mobileAimMode == "Toggle" then
        mobileAimToggleState = not mobileAimToggleState
        mobileAimActive = mobileAimToggleState
        updateAimBtnVisual()
    end
end)
AimBtn.MouseLeave:Connect(function()
    if mobileAimMode == "Hold" then
        mobileAimActive = false; updateAimBtnVisual()
    end
end)

local FARMspeed = 100

local function ensurePlayerInVehicle()
    if LP.Character then
        local vehicle = Workspace.Vehicles:FindFirstChild(LP.Name)
        local hum = LP.Character:FindFirstChild("Humanoid")
        if vehicle and hum and not hum.SeatPart then
            local ds = vehicle:FindFirstChild("DriveSeat")
            if ds then ds:Sit(hum) end
        end
    end
end

local function flyVehicleTo2(targetCFrame, callback)
    local vehicle = Workspace.Vehicles:FindFirstChild(LP.Name)
    if not vehicle then return end
    local driveSeat = vehicle:FindFirstChild("DriveSeat")
    local hum = LP.Character and LP.Character:FindFirstChild("Humanoid")
    if hum and driveSeat and not hum.SeatPart then driveSeat:Sit(hum) end
    if not vehicle.PrimaryPart then
        vehicle.PrimaryPart = vehicle:FindFirstChild("Mass", true)
    end
    if not vehicle.PrimaryPart then return end

    local startPos    = vehicle.PrimaryPart.Position
    local targetPos   = targetCFrame.Position
    local flightHeight = -1
    vehicle:SetPrimaryPartCFrame(CFrame.new(startPos.X, flightHeight, startPos.Z))
    local flightTarget = Vector3.new(targetPos.X, flightHeight, targetPos.Z)
    local distance = (Vector3.new(startPos.X, 0, startPos.Z) - Vector3.new(flightTarget.X, 0, flightTarget.Z)).Magnitude
    local duration = math.max(0.05, distance / FARMspeed)

    local info = TweenInfo.new(duration, Enum.EasingStyle.Linear)
    local CFv = Instance.new("CFrameValue")
    CFv.Value = vehicle:GetPrimaryPartCFrame()
    CFv:GetPropertyChangedSignal("Value"):Connect(function()
        local p = CFv.Value.Position
        vehicle:SetPrimaryPartCFrame(CFrame.new(p.X, flightHeight, p.Z))
        if vehicle.PrimaryPart then vehicle.PrimaryPart.Velocity = Vector3.zero end
    end)
    local tween = TweenService:Create(CFv, info, {Value = CFrame.new(flightTarget)})
    tween:Play()
    tween.Completed:Connect(function()
        CFv:Destroy()
        vehicle:SetPrimaryPartCFrame(targetCFrame)
        if callback then callback() end
    end)
end

local function teleportToLocation(coords, cb)
    ensurePlayerInVehicle()
    task.wait(0.5)
    flyVehicleTo2(coords, cb)
end

local function getSortedKeys(t)
    local keys = {}
    for k in pairs(t) do table.insert(keys, k) end
    table.sort(keys)
    return keys
end

local VehicleTab = Window:MakeTab({Name = "Vehicle", Icon = "rbxassetid://9033642906", PremiumOnly = false})

VehicleTab:AddSection({Name = "Vehicle Fly"})

local flightEnabled = false
local speed         = 150
local smoothPos     = 0.3
local smoothRot     = 0.2
local lastPos, lastLook

VehicleTab:AddToggle({
    Name = "Vehicle Fly", Default = false, Flag = "VehicleFlyToggle", Save = true,
    Callback = function(v) flightEnabled = v end
})

VehicleTab:AddBind({
    Name = "Vehicle Fly Keybind", Default = Enum.KeyCode.F, Hold = false,
    Flag = "VehicleFlyKeybind", Save = true,
    Callback = function() flightEnabled = not flightEnabled end
})

VehicleTab:AddToggle({
    Name = "Car Fly Mobile UI", Default = false, Flag = "CarFlyMobileUI", Save = true,
    Callback = function(v) MobileUI.Enabled = v end
})

local SafeFlyEnabled = false
local SafeFlyLoopThread
local LastSeat, LastVehicle = nil, nil

local function safeFlyReseat()
    local char = LP.Character; if not char then return end
    local hum  = char:FindFirstChildOfClass("Humanoid")
    local hrp  = char:FindFirstChild("HumanoidRootPart")
    if not (hum and hrp) then return end
    local seat = LastSeat
    if not (seat and seat.Parent) then
        local vehicles = Workspace:FindFirstChild("Vehicles")
        local vehicle  = vehicles and vehicles:FindFirstChild(LP.Name)
        seat = vehicle and vehicle:FindFirstChild("DriveSeat")
        if seat then LastVehicle = vehicle end
    end
    if seat and seat:IsA("BasePart") then
        pcall(function()
            hrp.CFrame = seat.CFrame
            hrp.AssemblyLinearVelocity  = Vector3.zero
            hrp.AssemblyAngularVelocity = Vector3.zero
            seat:Sit(hum)
        end)
    end
end

VehicleTab:AddToggle({
    Name = "Safe Fly (Auto Leave & Re-Enter every 10s)", Default = false, Flag = "SafeFly", Save = true,
    Callback = function(v)
        SafeFlyEnabled = v
        if v then
            SafeFlyLoopThread = task.spawn(function()
                while SafeFlyEnabled do
                    if flightEnabled and LP.Character then
                        local hum = LP.Character:FindFirstChildOfClass("Humanoid")
                        if hum and hum.SeatPart and hum.SeatPart.Name == "DriveSeat" then
                            LastSeat    = hum.SeatPart
                            LastVehicle = LastSeat.Parent
                            pcall(function() hum.Sit = false; hum:ChangeState(Enum.HumanoidStateType.Jumping) end)
                            task.wait(0.1)
                            if SafeFlyEnabled and flightEnabled then safeFlyReseat() end
                        end
                    end
                    task.wait(10)
                end
            end)
        else
            SafeFlyLoopThread = nil
        end
    end
})

VehicleTab:AddSlider({
    Name = "Flight Speed", Min = 50, Max = 400, Increment = 10, Default = 150,
    ValueName = "spd", Flag = "SpeedSlider", Save = true,
    Callback = function(v) speed = v end
})

RunService.RenderStepped:Connect(function(dt)
    if not flightEnabled then lastPos, lastLook = nil, nil; return end
    local char = LP.Character; if not char then return end
    local hum  = char:FindFirstChildOfClass("Humanoid")
    if not hum or not hum.SeatPart or hum.SeatPart.Name ~= "DriveSeat" then return end

    local seat = hum.SeatPart
    local vehicle = seat.Parent
    if not vehicle.PrimaryPart then vehicle.PrimaryPart = seat end

    local lookVector = Camera.CFrame.LookVector
    lastPos  = lastPos  or vehicle.PrimaryPart.Position
    lastLook = lastLook or lookVector

    local moveZ, moveY = 0, 0
    if UserInputService:IsKeyDown(Enum.KeyCode.W) or (seat:IsA("VehicleSeat") and seat.Throttle > 0) or mobileFwd then moveZ = 1 end
    if UserInputService:IsKeyDown(Enum.KeyCode.S) or (seat:IsA("VehicleSeat") and seat.Throttle < 0) or mobileBwd then moveZ = -1 end
    if UserInputService:IsKeyDown(Enum.KeyCode.E) or mobileUp   then moveY =  1 end
    if UserInputService:IsKeyDown(Enum.KeyCode.Q) or mobileDown then moveY = -1 end

    local targetPos = vehicle.PrimaryPart.Position
        + (lookVector * moveZ * speed * dt)
        + (Vector3.new(0, 1, 0) * moveY * speed * dt)

    local newPos     = lastPos:Lerp(targetPos, smoothPos)
    local smoothLook = lastLook:Lerp(lookVector, smoothRot)
    vehicle:SetPrimaryPartCFrame(CFrame.new(newPos, newPos + smoothLook))
    lastPos, lastLook = newPos, smoothLook

    for _, p in ipairs(vehicle:GetDescendants()) do
        if p:IsA("BasePart") then
            p.AssemblyLinearVelocity  = Vector3.zero
            p.AssemblyAngularVelocity = Vector3.zero
        end
    end
end)

VehicleTab:AddSection({Name = "Vehicle Visuals"})

local VehicleVisuals = {Rainbow = false, RainbowSpeed = 5, UseColor = false, Color = Color3.fromRGB(255,255,255)}

local function getVehicleBodyMesh()
    local vehicles = Workspace:FindFirstChild("Vehicles")
    local vehicle = vehicles and vehicles:FindFirstChild(LP.Name)
    if not vehicle then return nil end
    local bodyModel = vehicle:FindFirstChild("Body")
    if not bodyModel then return nil end
    local bodyMesh = bodyModel:FindFirstChild("Body")
    if bodyMesh and bodyMesh:IsA("BasePart") then return bodyMesh end
    return nil
end

VehicleTab:AddToggle({Name = "Rainbow Vehicle", Default = false, Flag = "RainbowVehicle", Save = true,
    Callback = function(v) VehicleVisuals.Rainbow = v end})
VehicleTab:AddSlider({Name = "Rainbow Speed", Min = 1, Max = 20, Increment = 1, Default = 5,
    ValueName = "x", Flag = "RainbowVehicleSpeed", Save = true,
    Callback = function(v) VehicleVisuals.RainbowSpeed = v end})
VehicleTab:AddToggle({Name = "Use Custom Color", Default = false, Flag = "UseVehicleColor", Save = true,
    Callback = function(v) VehicleVisuals.UseColor = v end})
VehicleTab:AddColorpicker({Name = "Custom Color", Default = Color3.fromRGB(255,255,255),
    Flag = "VehicleCustomColor", Save = true,
    Callback = function(v)
        VehicleVisuals.Color = v
    end})

VehicleTab:AddTextbox({Name = "Custom License Plate", Default = "", TextDisappear = false,
    Callback = function(text)
        local vehicles = Workspace:FindFirstChild("Vehicles")
        local vehicle = vehicles and vehicles:FindFirstChild(LP.Name)
        if vehicle then
            local body = vehicle:FindFirstChild("Body") or vehicle
            local plates = body:FindFirstChild("LicensePlates")
            if plates then
                for _, side in ipairs({"Front","Back"}) do
                    local s = plates:FindFirstChild(side)
                    if s and s:FindFirstChild("Gui") and s.Gui:FindFirstChild("TextLabel") then
                        s.Gui.TextLabel.Text = text
                    end
                end
            end
        end
    end})

RunService.RenderStepped:Connect(function()
    if not (VehicleVisuals.Rainbow or VehicleVisuals.UseColor) then return end
    local bodyMesh = getVehicleBodyMesh()
    if not bodyMesh then return end
    if VehicleVisuals.Rainbow then
        local hue = tick() * (VehicleVisuals.RainbowSpeed / 10) % 1
        bodyMesh.Color = Color3.fromHSV(hue, 1, 1)
    elseif VehicleVisuals.UseColor then
        bodyMesh.Color = VehicleVisuals.Color
    end
end)

VehicleTab:AddSection({Name = "Vehicle Mods"})

VehicleTab:AddButton({Name = "Bring own Car", Callback = function()
    local char = LP.Character or LP.CharacterAdded:Wait()
    local root = char:WaitForChild("HumanoidRootPart")
    local hum  = char:FindFirstChildWhichIsA("Humanoid")
    local vf = workspace:FindFirstChild("Vehicles")
    local vehicle = vf and vf:FindFirstChild(LP.Name)
    if vehicle and vehicle:IsA("Model") then
        local seat = vehicle:FindFirstChild("DriveSeat") or vehicle:FindFirstChildWhichIsA("VehicleSeat")
        if seat then
            if not vehicle.PrimaryPart then vehicle.PrimaryPart = seat end
            vehicle:SetPrimaryPartCFrame(root.CFrame * CFrame.new(0, 3, -8))
            task.wait(0.2)
            if hum and not hum.SeatPart then seat:Sit(hum) end
        end
    end
end})

VehicleTab:AddButton({Name = "Enter own Car", Callback = function()
    local vf = workspace:FindFirstChild("Vehicles")
    local vehicle = vf and vf:FindFirstChild(LP.Name)
    if vehicle and LP.Character then
        local hum = LP.Character:FindFirstChild("Humanoid")
        if hum and not hum.SeatPart then
            local ds = vehicle:FindFirstChild("DriveSeat")
            if ds then ds:Sit(hum) end
        end
    end
end})

local fuelToggle = false
VehicleTab:AddToggle({Name = "Infinite Fuel", Default = false, Flag = "InfiniteFuel", Save = true,
    Callback = function(v)
        fuelToggle = v
        if v then
            task.spawn(function()
                while fuelToggle do
                    local vf = workspace:FindFirstChild("Vehicles")
                    if vf then
                        local pv = vf:FindFirstChild(LP.Name)
                        if pv and pv:IsA("Model") then pv:SetAttribute("currentFuel", math.huge) end
                    end
                    task.wait(1)
                end
            end)
        end
    end})

local VehicleGodmodeEnabled = false
VehicleTab:AddToggle({Name = "Vehicle Godmode", Default = false, Flag = "VehicleGodmode", Save = true,
    Callback = function(v)
        VehicleGodmodeEnabled = v
        if v then
            task.spawn(function()
                while VehicleGodmodeEnabled do
                    local vf = workspace:FindFirstChild("Vehicles")
                    if vf then
                        local pv = vf:FindFirstChild(LP.Name)
                        if pv and pv:IsA("Model") then
                            pv:SetAttribute("IsOn", true)
                            pv:SetAttribute("currentHealth", 1000)
                            local hum = pv:FindFirstChildOfClass("Humanoid")
                            if hum then hum.MaxHealth = 1000; hum.Health = 1000 end
                        end
                    end
                    task.wait(1)
                end
            end)
        end
    end})

local jumpPower = 90
VehicleTab:AddSlider({Name = "Vehicle Jump Power", Min = 50, Max = 300, Increment = 10, Default = 90,
    ValueName = "pwr", Flag = "VehicleJumpPower", Save = true,
    Callback = function(v) jumpPower = v end})

VehicleTab:AddButton({Name = "Jump Vehicle", Callback = function()
    local vf = workspace:FindFirstChild("Vehicles"); if not vf then return end
    local v = vf:FindFirstChild(LP.Name)
    if v and v.PrimaryPart then
        v.PrimaryPart.AssemblyLinearVelocity = (v.PrimaryPart.CFrame.LookVector + Vector3.new(0,1.4,0)).Unit * jumpPower
    end
end})

local boostPower = 1000
VehicleTab:AddSlider({Name = "Boost Power", Min = 100, Max = 2000, Increment = 50, Default = 1000,
    ValueName = "pwr", Flag = "VehicleBoostPower", Save = true,
    Callback = function(v) boostPower = v end})

VehicleTab:AddButton({Name = "Boost Vehicle", Callback = function()
    local vf = workspace:FindFirstChild("Vehicles"); if not vf then return end
    local v = vf:FindFirstChild(LP.Name)
    if v and v.PrimaryPart then
        v.PrimaryPart.AssemblyLinearVelocity = v.PrimaryPart.CFrame.LookVector.Unit * boostPower
    end
end})

VehicleTab:AddSlider({Name = "Vehicle Height", Min = 1, Max = 13, Increment = 1, Default = 2,
    ValueName = "studs", Flag = "VehicleHeight", Save = true,
    Callback = function(v)
        local vf = workspace:FindFirstChild("Vehicles"); if not vf then return end
        local vehicle = vf:FindFirstChild(LP.Name); if not vehicle then return end
        local ds = vehicle:FindFirstChild("DriveSeat", true); if not ds then return end
        for _, c in pairs(ds:GetChildren()) do
            if c:IsA("SpringConstraint") then
                c.LimitsEnabled = true; c.MinLength = v; c.MaxLength = v
            elseif c:IsA("RopeConstraint") then
                c.Length = v
            end
        end
    end})

VehicleTab:AddSection({Name = "Tuning"})
for _, name in ipairs({"armorLevel","brakesLevel","engineLevel"}) do
    local label = ({armorLevel="Armor", brakesLevel="Brakes", engineLevel="Engine"})[name]
    VehicleTab:AddSlider({Name = label .. " Level", Min = 0, Max = 6, Increment = 1, Default = 0,
        ValueName = "lvl", Flag = "Tune_"..name, Save = true,
        Callback = function(v)
            local vehicle = Workspace.Vehicles:FindFirstChild(LP.Name)
            if vehicle then vehicle:SetAttribute(name, v) end
        end})
end

local TeleportTab = Window:MakeTab({Name = "Teleport", Icon = "rbxassetid://6723742952", PremiumOnly = false})

local robberyLocations = {
    ["Bank"] = CFrame.new(-1183.296, 10.912, 3228.297),
    ["Jewellery"] = CFrame.new(-407.536, 21.950, 3516.854),
    ["Erwin Club"] = CFrame.new(-1856.962, 5.706, 2990.518),
    ["Gas-N-Go Fuel"] = CFrame.new(-1560.674, 3.944, 3813.656),
    ["Ares Fuel"] = CFrame.new(-824.447, 4.182, 1512.941),
    ["Osso Fuel"] = CFrame.new(-27.464, 5.245, -749.413),
    ["Container Ship"] = CFrame.new(1191.836, 29.550, 2140.703),
    ["Clothing Store"] = CFrame.new(440.400, 5.520, -1438.111),
}
local jobLocations = {
    ["Bus Company"] = CFrame.new(-1682.297, 8.779, -1273.078),
    ["Truck Company"] = CFrame.new(704.451, 4.229, 1479.927),
    ["Fire Station"] = CFrame.new(-1025.361, 4.500, 3899.155),
    ["Adac"] = CFrame.new(-120.607, 4.597, 430.818),
}
local otherLocations = {
    ["Police"] = CFrame.new(-1565.78, 5.64, 2733.50),
    ["Seve Point"] = CFrame.new(-2159.29, -24.17, 2164.24),
    ["Tool Shop"] = CFrame.new(-767.815, 4.374, 663.494),
    ["Farm Shop"] = CFrame.new(-887.220, 5.831, -1150.356),
    ["Prison Out"] = CFrame.new(-615.579, 5.289, 2862.236),
    ["Prison In"] = CFrame.new(-572.105, 6.382, 3061.374),
    ["Hospital"] = CFrame.new(-278.834, 7.745, 1085.797),
    ["Tuning Garage"] = CFrame.new(-1461.075, 5.610, 170.780),
    ["Dealership"] = CFrame.new(-1415.699, 4.552, 940.526),
    ["Smuggler"] = CFrame.new(796.557, -18.670, -1526.379),
}

TeleportTab:AddSlider({Name = "Teleport Speed", Min = 50, Max = 300, Increment = 10, Default = 100,
    ValueName = "spd", Flag = "TeleportSpeed", Save = true,
    Callback = function(v) FARMspeed = v end})

TeleportTab:AddDropdown({Name = "Robbery Places", Options = getSortedKeys(robberyLocations),
    Default = "Bank", Flag = "TPRobbery", Save = false,
    Callback = function(opt)
        if not UI_READY or LoadingConfig then return end
        if robberyLocations[opt] then teleportToLocation(robberyLocations[opt]) end
    end})

TeleportTab:AddDropdown({Name = "Job Places", Options = getSortedKeys(jobLocations),
    Default = "Bus Company", Flag = "TPJob", Save = false,
    Callback = function(opt)
        if not UI_READY or LoadingConfig then return end
        if jobLocations[opt] then teleportToLocation(jobLocations[opt]) end
    end})

TeleportTab:AddDropdown({Name = "Other Places", Options = getSortedKeys(otherLocations),
    Default = "Hospital", Flag = "TPOther", Save = false,
    Callback = function(opt)
        if not UI_READY or LoadingConfig then return end
        if otherLocations[opt] then teleportToLocation(otherLocations[opt]) end
    end})

TeleportTab:AddTextbox({Name = "Custom Coords (X, Y, Z)", Default = "", TextDisappear = false,
    Callback = function(text)
        local x, y, z = text:match("([^,]+),%s*([^,]+),%s*([^,]+)")
        if x and y and z then
            teleportToLocation(CFrame.new(tonumber(x), tonumber(y), tonumber(z)))
        else
            notify("Error", "Invalid Format! Use: X, Y, Z", 2)
        end
    end})

TeleportTab:AddButton({Name = "TP Nearest Dealer", Callback = function()
    local char = LP.Character; if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local closest, minDist = nil, math.huge
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("Model") and (obj.Name:lower():find("dealer") or obj.Name:lower():find("smuggler")) then
            local r = obj.PrimaryPart or obj:FindFirstChild("HumanoidRootPart") or obj:FindFirstChildWhichIsA("BasePart")
            if r then
                local d = (char.HumanoidRootPart.Position - r.Position).Magnitude
                if d < minDist then minDist, closest = d, {Name = obj.Name, CFrame = r.CFrame} end
            end
        end
    end
    if closest then
        notify("Teleporting", "Closest dealer: " .. closest.Name, 2)
        teleportToLocation(closest.CFrame)
    end
end})

TeleportTab:AddButton({Name = "TP Nearest Vending Machine", Callback = function()
    local char = LP.Character; if not char or not char:FindFirstChild("HumanoidRootPart") then return end
    local vm = workspace:FindFirstChild("Robberies") and workspace.Robberies:FindFirstChild("VendingMachines")
    if not vm then notify("Error", "VendingMachines not found", 3); return end
    local closestCF, minDist = nil, math.huge
    for _, obj in pairs(vm:GetChildren()) do
        if obj:IsA("Model") then
            local r = obj.PrimaryPart or obj:FindFirstChildWhichIsA("BasePart")
            if r then
                local d = (char.HumanoidRootPart.Position - r.Position).Magnitude
                if d < minDist then minDist, closestCF = d, r.CFrame end
            end
        end
    end
    if closestCF then
        teleportToLocation(closestCF * CFrame.new(0,0,4))
        notify("Teleported", "Flying to vending machine", 2)
    else
        notify("Error", "No vending machines found", 3)
    end
end})

local PoliceTab = Window:MakeTab({Name = "Police", Icon = "rbxassetid://17169180878", PremiumOnly = false})

local function isWantedPlayer(plr)
    if not plr.Character then return false end
    local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
    if hrp then
        for _, n in ipairs({"IsWanted","Wanted","WantedLevel","WantedStars"}) do
            local v = hrp:GetAttribute(n)
            if v and v ~= false and v ~= 0 then return true end
        end
    end
    for _, n in ipairs({"IsWanted","Wanted","WantedLevel","WantedStars"}) do
        local o = plr.Character:FindFirstChild(n)
        if o and o.Value and o.Value ~= false and o.Value ~= 0 then return true end
    end
    return false
end

local function GetWantedPlayers()
    local t = {}
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LP and isWantedPlayer(plr) then table.insert(t, plr.Name) end
    end
    if #t == 0 then table.insert(t, "None") end
    return t
end

local SelectedWanted = nil
PoliceTab:AddDropdown({Name = "Select Wanted Player", Options = GetWantedPlayers(),
    Default = "None", Flag = "WantedDropdown", Save = false,
    Callback = function(opt) SelectedWanted = opt end})

PoliceTab:AddButton({Name = "Teleport to Wanted", Callback = function()
    if SelectedWanted and SelectedWanted ~= "None" then
        local t = Players:FindFirstChild(SelectedWanted)
        if t and t.Character and t.Character:FindFirstChild("HumanoidRootPart") then
            teleportToLocation(t.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, 5))
            notify("Teleported", "Flying to " .. SelectedWanted, 2)
        else
            notify("Error", "Player not spawned", 2)
        end
    else
        notify("Error", "No wanted player selected", 2)
    end
end})

PoliceTab:AddSection({Name = "Auto Tools"})

local RadarFarmEnabled = false
PoliceTab:AddToggle({Name = "Radar Farm", Default = false, Flag = "RadarFarm", Save = true,
    Callback = function(v)
        RadarFarmEnabled = v
        if v then
            task.spawn(function()
                while RadarFarmEnabled do
                    local char = LP.Character
                    if char then
                        local radar  = char:FindFirstChild("Radar Gun")
                        local folder = ReplicatedStorage:FindFirstChild("GpP")
                        local remote = folder and folder:FindFirstChild("6f5f6fc7-bcb9-46cd-92ca-8c7e2d06bc11")
                        local hrp    = char:FindFirstChild("HumanoidRootPart")
                        if radar and remote and hrp then
                            local vehicles = Workspace:FindFirstChild("Vehicles")
                            if vehicles then
                                for _, veh in ipairs(vehicles:GetChildren()) do
                                    local ds = veh:FindFirstChild("DriveSeat")
                                    if ds and veh ~= vehicles:FindFirstChild(LP.Name) then
                                        local pos = ds.Position
                                        local dir = (pos - hrp.Position).Unit
                                        pcall(function() remote:FireServer(radar, pos, dir) end)
                                    end
                                end
                            end
                        end
                    end
                    task.wait(1)
                end
            end)
        end
    end})

local AutoTaserEnabled    = false
local AutoTaserWallCheck  = true
PoliceTab:AddToggle({Name = "Auto Taser", Default = false, Flag = "AutoTaser", Save = true,
    Callback = function(v) AutoTaserEnabled = v end})
PoliceTab:AddToggle({Name = "Auto Taser Wall Check", Default = true, Flag = "AutoTaserWallCheck", Save = true,
    Callback = function(v) AutoTaserWallCheck = v end})

local AntiCuffEnabled = false
PoliceTab:AddToggle({Name = "Anti Cuff", Default = false, Flag = "AntiCuff", Save = true,
    Callback = function(v) AntiCuffEnabled = v end})

local TeaserShootRemote
task.spawn(function()
    local f = ReplicatedStorage:WaitForChild("GpP", 5)
    if f then TeaserShootRemote = f:FindFirstChild("664bcd31-3ad5-470e-87db-ab2f85f6fa5c") end
end)

RunService.Heartbeat:Connect(function()
    if AntiCuffEnabled and LP.Character then
        local hrp = LP.Character:FindFirstChild("HumanoidRootPart")
        local hum = LP.Character:FindFirstChild("Humanoid")
        if hrp and hum then
            for _, plr in pairs(Players:GetPlayers()) do
                if plr ~= LP and plr.Team and plr.Team.Name == "Police" and plr.Character then
                    local phrp = plr.Character:FindFirstChild("HumanoidRootPart")
                    if phrp then
                        local d = (hrp.Position - phrp.Position).Magnitude
                        if d < 25 and not hum.SeatPart then
                            local v = Workspace.Vehicles:FindFirstChild(LP.Name)
                            if v then
                                local seat = v:FindFirstChild("DriveSeat")
                                if seat and (seat.Position - hrp.Position).Magnitude < 60 then seat:Sit(hum) end
                            end
                        end
                        if d < 10 then
                            hrp.CFrame = CFrame.new(hrp.Position + (hrp.Position - phrp.Position).Unit * 2)
                        end
                    end
                end
            end
        end
    end
end)

RunService.Heartbeat:Connect(function()
    if not AutoTaserEnabled then return end
    local char = LP.Character; if not char then return end
    local taser = char:FindFirstChild("Taser"); if not taser then return end
    local hrp = char:FindFirstChild("HumanoidRootPart"); if not hrp then return end
    local best, closest = nil, 20
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LP and plr.Character then
            local p = plr.Character:FindFirstChild("HumanoidRootPart")
            local h = plr.Character:FindFirstChild("Humanoid")
            if p and h and h.Health > 0 and p:GetAttribute("IsWanted") and not h.Sit then
                local d = (hrp.Position - p.Position).Magnitude
                if d < closest then
                    local visible = true
                    if AutoTaserWallCheck then
                        local rp = RaycastParams.new()
                        rp.FilterType = Enum.RaycastFilterType.Blacklist
                        rp.FilterDescendantsInstances = {char}
                        local r = Workspace:Raycast(hrp.Position, p.Position - hrp.Position, rp)
                        if r and not r.Instance:IsDescendantOf(plr.Character) then visible = false end
                    end
                    if visible then closest, best = d, p end
                end
            end
        end
    end
    if best and TeaserShootRemote then
        local pp = best.Position + (best.Velocity * 0.22)
        TeaserShootRemote:FireServer(taser, pp, (pp - hrp.Position).Unit)
    end
end)

local RobTab = Window:MakeTab({Name = "Rob", Icon = "rbxassetid://7733964640", PremiumOnly = false})

RobTab:AddSection({Name = "Bank"})

local BankStatusLabel = RobTab:AddLabel("Bank: Checking...")
local BankNotifyOnOpen = false
local BankWasOpen = nil

local function getBankLight()
    local rob = Workspace:FindFirstChild("Robberies")
    local bank = rob and rob:FindFirstChild("BankRobbery")
    local light = bank and bank:FindFirstChild("LightGreen")
    if light and light:IsA("BasePart") then return light end
    return nil
end

local function isBankOpen()
    local light = getBankLight()
    if not light then return nil end
    local c = light.Color
    return math.floor(c.R * 255 + 0.5) == 73
       and math.floor(c.G * 255 + 0.5) == 147
       and math.floor(c.B * 255 + 0.5) == 0
end

local function updateBankLabel()
    local state = isBankOpen()
    local text
    if state == nil then
        text = "Bank: Not Found"
    elseif state then
        text = "Bank: OPEN"
    else
        text = "Bank: CLOSED"
    end
    pcall(function() BankStatusLabel:Set(text) end)
    if BankNotifyOnOpen and state == true and BankWasOpen == false then
        notify("Bank Robbery", "Bank is now OPEN!", 4)
    end
    if state ~= nil then BankWasOpen = state end
end

RobTab:AddToggle({Name = "Notify when Bank opens", Default = false, Flag = "BankNotifyOnOpen", Save = true,
    Callback = function(v) BankNotifyOnOpen = v end})

RobTab:AddButton({Name = "Refresh Bank Status", Callback = updateBankLabel})

RobTab:AddSection({Name = "Jeweler"})

local JewelerStatusLabel = RobTab:AddLabel("Robbable: 0")

local function countJewelerRobbables()
    local rob = Workspace:FindFirstChild("Robberies")
    local jeweler = rob and rob:FindFirstChild("Jeweler Robbery")
    local robbables = jeweler and jeweler:FindFirstChild("Robbables")
    if not robbables then return nil end
    local count = 0
    for _, item in ipairs(robbables:GetChildren()) do
        local glass = item:FindFirstChild("BrokenGlass")
        if glass and glass:IsA("BasePart") and glass.Transparency == 1 then
            count = count + 1
        end
    end
    return count
end

local function updateJewelerLabel()
    local n = countJewelerRobbables()
    local text = (n == nil) and "Robbable: Not Found" or ("Robbable: " .. tostring(n))
    pcall(function() JewelerStatusLabel:Set(text) end)
end

RobTab:AddButton({Name = "Refresh Jeweler Status", Callback = updateJewelerLabel})

task.spawn(function()
    while task.wait(1) do
        pcall(updateBankLabel)
        pcall(updateJewelerLabel)
    end
end)

local CombatTab = Window:MakeTab({Name = "Combat", Icon = "rbxassetid://80964454894155", PremiumOnly = false})

CombatTab:AddSection({Name = "Aimbot"})

local AimbotEnabled    = false
local AimPart          = "Head"
local AimbotTeamCheck  = true
local AimbotSmoothness = 0.2
local PredictionEnabled= true
local AimbotWallCheck  = false
local KnockThreshold   = 24
local MaxDistance      = 500
local FOVSize          = 100
local FOVColor         = Color3.fromRGB(255, 255, 255)
local IgnoreUntouchable= true
local IgnoreNotWanted  = false
local WhitelistedUsers = {}
local IgnoredTeams = {ADAC=true, BusCompany=true, FireDepartment=true, Prisoner=true, TruckCompany=true}

CombatTab:AddToggle({Name = "Enable Aimbot", Default = false, Flag = "AimbotEnabled", Save = true,
    Callback = function(v) AimbotEnabled = v end})
CombatTab:AddBind({Name = "Aimbot Toggle Key", Default = Enum.KeyCode.L, Hold = false,
    Flag = "AimbotKeybind", Save = true,
    Callback = function() AimbotEnabled = not AimbotEnabled end})
CombatTab:AddToggle({Name = "Aimbot Mobile UI", Default = false, Flag = "AimbotMobileUI", Save = true,
    Callback = function(v) AimMobileUI.Enabled = v end})
CombatTab:AddDropdown({Name = "Mobile Aim Mode", Options = {"Hold","Toggle"}, Default = "Hold",
    Flag = "AimMobileMode", Save = true,
    Callback = function(opt)
        mobileAimMode = opt
        if opt == "Hold" then
            mobileAimActive = false; mobileAimToggleState = false; updateAimBtnVisual()
        end
    end})
CombatTab:AddDropdown({Name = "Aim Style", Options = {"Normal","Sticky"}, Default = "Normal",
    Flag = "AimStyle", Save = true,
    Callback = function(opt) mobileAimStyle = opt; if opt == "Normal" then stickyTarget = nil end end})
CombatTab:AddDropdown({Name = "Aim Part", Options = {"Head","HumanoidRootPart"}, Default = "Head",
    Flag = "AimPartAimbot", Save = true, Callback = function(opt) AimPart = opt end})
CombatTab:AddToggle({Name = "Ignore Team", Default = false, Flag = "IgnoreTeamAimbot", Save = true,
    Callback = function(v) AimbotTeamCheck = v end})
CombatTab:AddToggle({Name = "Wall Check", Default = false, Flag = "AimbotWallCheck", Save = true,
    Callback = function(v) AimbotWallCheck = v end})
CombatTab:AddToggle({Name = "Hit Prediction", Default = true, Flag = "HitPredictionAimbot", Save = true,
    Callback = function(v) PredictionEnabled = v end})
CombatTab:AddToggle({Name = "Ignore Untouchable Teams", Default = true, Flag = "IgnoreUntouchableTeams", Save = true,
    Callback = function(v) IgnoreUntouchable = v end})
CombatTab:AddToggle({Name = "Ignore Not Wanted Civilians", Default = false, Flag = "IgnoreNotWantedCivilians", Save = true,
    Callback = function(v) IgnoreNotWanted = v end})
CombatTab:AddColorpicker({Name = "FOV Color", Default = Color3.fromRGB(255,255,255),
    Flag = "FOVColorAimbot", Save = true, Callback = function(v) FOVColor = v end})
CombatTab:AddSlider({Name = "Max Distance", Min = 10, Max = 1000, Increment = 10, Default = 500,
    ValueName = "studs", Flag = "MaxDistance", Save = true,
    Callback = function(v) MaxDistance = v end})
CombatTab:AddSlider({Name = "Aimbot Smoothness", Min = 1, Max = 10, Increment = 1, Default = 2,
    ValueName = "x", Flag = "AimbotSmoothness", Save = true,
    Callback = function(v) AimbotSmoothness = v / 10 end})
CombatTab:AddSlider({Name = "FOV Size", Min = 20, Max = 300, Increment = 5, Default = 100,
    ValueName = "px", Flag = "FOVSizeAimbot", Save = true,
    Callback = function(v) FOVSize = v end})

local FOVCircle = Drawing.new("Circle")
FOVCircle.Color = FOVColor; FOVCircle.Thickness = 1; FOVCircle.NumSides = 60
FOVCircle.Radius = FOVSize; FOVCircle.Visible = false; FOVCircle.Filled = false
FOVCircle.Transparency = 1

local function predictTarget(target, t)
    if not target.Character or not target.Character:FindFirstChild(AimPart) then return Vector3.zero end
    return target.Character[AimPart].Position + target.Character[AimPart].Velocity * t
end

local function getClosestTarget()
    local cam = Workspace.CurrentCamera
    local closestPlr, closestDist = nil, math.huge
    if not LP.Character or not LP.Character:FindFirstChild("HumanoidRootPart") then return nil end
    local localPos  = LP.Character.HumanoidRootPart.Position
    local localTeam = LP.Team
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LP and plr.Character and plr.Character:FindFirstChild(AimPart) and not WhitelistedUsers[plr.Name] then
            local skip = false
            if IgnoreUntouchable and plr.Team and IgnoredTeams[plr.Team.Name] then skip = true end
            if not skip and AimbotTeamCheck then
                if not localTeam or not plr.Team then skip = true end
                if not skip then
                    if localTeam.Name == "Citizen" and plr.Team.Name ~= "Police" then skip = true end
                    if localTeam.Name == "Police"  and plr.Team.Name ~= "Citizen" then skip = true end
                    if localTeam == plr.Team then skip = true end
                end
            end
            if not skip and IgnoreNotWanted then
                local hrp = plr.Character:FindFirstChild("HumanoidRootPart")
                local wanted = hrp and hrp:GetAttribute("IsWanted") == true
                local police = plr.Team and plr.Team.Name == "Police"
                if not wanted and not police then skip = true end
            end
            if not skip and AimbotWallCheck then
                local tp = plr.Character:FindFirstChild(AimPart)
                if tp then
                    local rp = RaycastParams.new()
                    rp.FilterType = Enum.RaycastFilterType.Blacklist
                    rp.FilterDescendantsInstances = {LP.Character}
                    local r = Workspace:Raycast(cam.CFrame.Position, tp.Position - cam.CFrame.Position, rp)
                    if r and not r.Instance:IsDescendantOf(plr.Character) then skip = true end
                end
            end
            if not skip then
                local hum = plr.Character:FindFirstChildOfClass("Humanoid")
                if not hum or hum.Health <= KnockThreshold then skip = true end
                if not skip then
                    local pos = plr.Character[AimPart].Position
                    if (localPos - pos).Magnitude > MaxDistance then skip = true end
                    if not skip then
                        local sp = cam:WorldToScreenPoint(pos)
                        if sp.Z < 0 then skip = true end
                        if not skip then
                            local sd = (Vector2.new(sp.X, sp.Y) - Vector2.new(cam.ViewportSize.X/2, cam.ViewportSize.Y/2)).Magnitude
                            if sd <= FOVSize and sd < closestDist then
                                closestDist, closestPlr = sd, plr
                            end
                        end
                    end
                end
            end
        end
    end
    return closestPlr
end

local prevAimActive = false
RunService.RenderStepped:Connect(function()
    FOVCircle.Position = Vector2.new(Workspace.CurrentCamera.ViewportSize.X/2, Workspace.CurrentCamera.ViewportSize.Y/2)
    FOVCircle.Radius = FOVSize
    FOVCircle.Color  = FOVColor
    FOVCircle.Visible = AimbotEnabled

    local cam = Workspace.CurrentCamera
    local isAiming = AimbotEnabled and (UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) or mobileAimActive)

    if mobileAimStyle == "Sticky" then
        if isAiming and not prevAimActive then stickyTarget = getClosestTarget()
        elseif not isAiming then stickyTarget = nil end
    else
        stickyTarget = nil
    end
    prevAimActive = isAiming

    local target = (mobileAimStyle == "Sticky" and stickyTarget) or getClosestTarget()
    if not target or not target.Character or not target.Character:FindFirstChild(AimPart) then return end
    if isAiming then
        local pos = PredictionEnabled and predictTarget(target, 0.2) or target.Character[AimPart].Position
        local dir = (pos - cam.CFrame.Position).Unit
        local nl  = cam.CFrame.LookVector:Lerp(dir, AimbotSmoothness)
        cam.CFrame = CFrame.new(cam.CFrame.Position, cam.CFrame.Position + nl)
    end
end)

local GunTab = Window:MakeTab({Name = "Gun", Icon = "rbxassetid://15571374043", PremiumOnly = false})

local NoRecoilEnabled, FastBulletEnabled, AutoReloadEnabled = false, false, false
local CrosshairSize, AimFOV = 25, 40

GunTab:AddToggle({Name = "No Recoil",  Default = false, Flag = "NoRecoilToggle",  Save = true,
    Callback = function(v) NoRecoilEnabled = v end})
GunTab:AddToggle({Name = "Fast Bullet", Default = false, Flag = "FastBullet", Save = true,
    Callback = function(v) FastBulletEnabled = v end})
GunTab:AddToggle({Name = "Auto Reload", Default = false, Flag = "AutoReload", Save = true,
    Callback = function(v) AutoReloadEnabled = v end})
GunTab:AddSlider({Name = "Crosshair Size", Min = 0, Max = 50, Increment = 1, Default = 25,
    ValueName = "px", Flag = "CrosshairSize", Save = true, Callback = function(v) CrosshairSize = v end})
GunTab:AddSlider({Name = "Aim FOV", Min = 10, Max = 120, Increment = 1, Default = 40,
    ValueName = "fov", Flag = "AimFOV", Save = true, Callback = function(v) AimFOV = v end})

RunService.Heartbeat:Connect(function()
    if FastBulletEnabled and LP.Character then
        local tool = LP.Character:FindFirstChildOfClass("Tool")
        if tool then
            tool:SetAttribute("ShootDelay", 0)
            tool:SetAttribute("Automatic", true)
        end
    end
end)

task.spawn(function()
    local VIM = game:GetService("VirtualInputManager")
    while true do
        if AutoReloadEnabled and LP.Character then
            local tool = LP.Character:FindFirstChildOfClass("Tool")
            if tool then
                local mag = tool:GetAttribute("MagCurrentSize") or tool:GetAttribute("Ammo") or tool:GetAttribute("Clip")
                if mag and mag == 0 then
                    VIM:SendKeyEvent(true,  Enum.KeyCode.R, false, game)
                    task.wait(0.05)
                    VIM:SendKeyEvent(false, Enum.KeyCode.R, false, game)
                    task.wait(1)
                end
            end
        end
        task.wait(0.5)
    end
end)

RunService.Heartbeat:Connect(function()
    if LP.Character then
        local tool = LP.Character:FindFirstChildOfClass("Tool")
        if tool then
            tool:SetAttribute("AimFieldOfView", AimFOV)
            tool:SetAttribute("CrosshairSize", CrosshairSize)
        end
    end
end)

pcall(function()
    local mt = getrawmetatable(game)
    if mt then
        local oldIndex = mt.__index
        setreadonly(mt, false)
        mt.__index = newcclosure(function(self, key)
            if NoRecoilEnabled and not checkcaller() and (key == "Recoil" or key == "RecoilMin" or key == "RecoilMax") then
                return 0
            end
            return oldIndex(self, key)
        end)
        setreadonly(mt, true)
    end
end)

GunTab:AddSection({Name = "Gun Visuals"})
local GunVisuals = {Rainbow=false, RainbowSpeed=5, UseColor=false, Color=Color3.fromRGB(255,255,255),
    Transparency=0, Reflectance=0, Material="Default", HideTextures=false}
GunTab:AddToggle({Name = "Rainbow Gun", Default = false, Flag = "RainbowGun", Save = true,
    Callback = function(v) GunVisuals.Rainbow = v end})
GunTab:AddSlider({Name = "Rainbow Speed", Min = 1, Max = 20, Increment = 1, Default = 5,
    ValueName = "x", Flag = "RainbowGunSpeed", Save = true,
    Callback = function(v) GunVisuals.RainbowSpeed = v end})
GunTab:AddToggle({Name = "Use Custom Color", Default = false, Flag = "UseGunColor", Save = true,
    Callback = function(v) GunVisuals.UseColor = v end})
GunTab:AddColorpicker({Name = "Gun Color", Default = Color3.fromRGB(255,255,255),
    Flag = "GunColor", Save = true, Callback = function(v) GunVisuals.Color = v end})
GunTab:AddDropdown({Name = "Gun Material",
    Options = {"Default","Plastic","Neon","Glass","ForceField","Metal","Foil"}, Default = "Default",
    Flag = "GunMaterial", Save = true, Callback = function(v) GunVisuals.Material = v end})
GunTab:AddToggle({Name = "Hide Textures", Default = false, Flag = "GunNoTexture", Save = true,
    Callback = function(v) GunVisuals.HideTextures = v end})
GunTab:AddSlider({Name = "Transparency", Min = 0, Max = 10, Increment = 1, Default = 0,
    ValueName = "/10", Flag = "GunTransparency", Save = true,
    Callback = function(v) GunVisuals.Transparency = v / 10 end})
GunTab:AddSlider({Name = "Reflectance", Min = 0, Max = 10, Increment = 1, Default = 0,
    ValueName = "/10", Flag = "GunReflectance", Save = true,
    Callback = function(v) GunVisuals.Reflectance = v / 10 end})

RunService.RenderStepped:Connect(function()
    if not LP.Character then return end
    local tool = LP.Character:FindFirstChildOfClass("Tool"); if not tool then return end
    local applyColor, color = false, GunVisuals.Color
    if GunVisuals.Rainbow then
        local hue = tick() * (GunVisuals.RainbowSpeed/10) % 1
        color = Color3.fromHSV(hue, 1, 1); applyColor = true
    elseif GunVisuals.UseColor then applyColor = true end
    if applyColor or GunVisuals.Material ~= "Default" or GunVisuals.Transparency > 0 or GunVisuals.Reflectance > 0 or GunVisuals.HideTextures then
        for _, p in pairs(tool:GetDescendants()) do
            if p:IsA("BasePart") then
                if applyColor then p.Color = color end
                if GunVisuals.Material ~= "Default" then p.Material = Enum.Material[GunVisuals.Material] end
                if GunVisuals.Transparency > 0 then p.Transparency = GunVisuals.Transparency end
                if GunVisuals.Reflectance > 0 then p.Reflectance = GunVisuals.Reflectance end
                if GunVisuals.HideTextures and p:IsA("MeshPart") then p.TextureID = "" end
            end
        end
    end
end)

local ESPTab = Window:MakeTab({Name = "ESP", Icon = "rbxassetid://13321848320", PremiumOnly = false})

local ESPEnabled = false
local ESPSettings = {
    ShowName = true, ShowUsername = true, ShowHealth = true,
    ShowDistance = true, ShowTeam = true, ShowWanted = true,
    TeamCheck = false, Tracer = false,
    TracerThickness = 1, TracerTransparency = 0.5
}

local ESPHolder = Instance.new("ScreenGui")
ESPHolder.Name = "Xeioa_ESP_Holder"
ESPHolder.ResetOnSpawn = false
ESPHolder.IgnoreGuiInset = true
ESPHolder.Parent = LP:WaitForChild("PlayerGui")

local ESPData = {}

local function clearPlayerESP(plr)
    local d = ESPData[plr]
    if not d then return end
    if d.bb     then d.bb:Destroy()                       end
    if d.tracer then pcall(function() d.tracer:Remove() end) end
    ESPData[plr] = nil
end

local function setupPlayer(plr)
    if plr == LP then return end
    if ESPData[plr] then return end

    local bb = Instance.new("BillboardGui")
    bb.Name = "ESP_" .. plr.Name
    bb.Size = UDim2.new(0, 200, 0, 90)
    bb.StudsOffset = Vector3.new(0, 3, 0)
    bb.AlwaysOnTop = true
    bb.LightInfluence = 0
    bb.Enabled = false
    bb.Parent = ESPHolder

    local lbl = Instance.new("TextLabel")
    lbl.Parent = bb
    lbl.Size = UDim2.new(1, 0, 1, 0)
    lbl.BackgroundTransparency = 1
    lbl.TextColor3 = Color3.new(1, 1, 1)
    lbl.TextStrokeTransparency = 0.5
    lbl.TextSize = 12
    lbl.Font = Enum.Font.GothamBold
    lbl.RichText = true

    local tracer = Drawing.new("Line")
    tracer.Visible = false
    tracer.Color = Color3.new(1, 1, 1)
    tracer.Thickness = ESPSettings.TracerThickness
    tracer.Transparency = ESPSettings.TracerTransparency

    ESPData[plr] = {bb = bb, label = lbl, tracer = tracer}
end

for _, plr in ipairs(Players:GetPlayers()) do setupPlayer(plr) end
Players.PlayerAdded:Connect(setupPlayer)
Players.PlayerRemoving:Connect(clearPlayerESP)

RunService.RenderStepped:Connect(function()
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr == LP then continue end
        if not ESPData[plr] then setupPlayer(plr) end
        local d = ESPData[plr]
        if not d then continue end
        local bb, lbl, tracer = d.bb, d.label, d.tracer

        if not ESPEnabled then bb.Enabled = false; tracer.Visible = false; continue end

        local char = plr.Character
        local head = char and char:FindFirstChild("Head")
        local hrp  = char and char:FindFirstChild("HumanoidRootPart")
        local hum  = char and char:FindFirstChildOfClass("Humanoid")

        if not (char and head and hrp) then
            bb.Adornee = nil; bb.Enabled = false; tracer.Visible = false; continue
        end
        if hum and hum.Health <= 0 then
            bb.Enabled = false; tracer.Visible = false; continue
        end
        if ESPSettings.TeamCheck and LP.Team and plr.Team and LP.Team == plr.Team then
            bb.Enabled = false; tracer.Visible = false; continue
        end

        bb.Adornee = head
        bb.Enabled = true

        local lphrp = LP.Character and LP.Character:FindFirstChild("HumanoidRootPart")
        local dist  = lphrp and math.floor((hrp.Position - lphrp.Position).Magnitude) or 0
        local hp    = hum and math.floor(hum.Health) or 0
        local maxhp = hum and math.floor(hum.MaxHealth) or 100
        local team  = plr.Team and plr.Team.Name or "No Team"
        local hex   = plr.TeamColor and plr.TeamColor.Color:ToHex() or "FFFFFF"

        local parts = {}
        if ESPSettings.ShowName     then table.insert(parts, "<font color='#"..hex.."'>"..plr.DisplayName.."</font>") end
        if ESPSettings.ShowUsername then table.insert(parts, "<font size='10' color='#AAAAAA'>@"..plr.Name.."</font>") end
        if ESPSettings.ShowHealth   then
            local r = math.floor((1 - (hp / math.max(maxhp,1))) * 255)
            local g = math.floor((hp / math.max(maxhp,1)) * 255)
            table.insert(parts, "HP: <font color='#"..string.format("%02x%02x00", r, g).."'>"..hp.."/"..maxhp.."</font>")
        end
        if ESPSettings.ShowDistance then table.insert(parts, "<font size='10'>"..dist.." studs</font>") end
        if ESPSettings.ShowTeam     then table.insert(parts, "<font size='10' color='#"..hex.."'>"..team.."</font>") end
        if ESPSettings.ShowWanted   then
            local w = hrp:GetAttribute("IsWanted") == true
                or hrp:GetAttribute("Wanted") == true
                or (hrp:GetAttribute("WantedLevel") or 0) > 0
                or (hrp:GetAttribute("WantedStars") or 0) > 0
            if w then table.insert(parts, "<font color='#FFD700'>WANTED</font>") end
        end
        lbl.Text = table.concat(parts, "\n")

        if ESPSettings.Tracer then
            local vp = Camera.ViewportSize
            local sp, on = Camera:WorldToViewportPoint(hrp.Position)
            if on and sp.Z > 0 then
                tracer.Visible      = true
                tracer.Thickness    = ESPSettings.TracerThickness
                tracer.Transparency = ESPSettings.TracerTransparency
                tracer.From = Vector2.new(vp.X / 2, vp.Y)
                tracer.To   = Vector2.new(sp.X, sp.Y)
                if plr.TeamColor then tracer.Color = plr.TeamColor.Color end
            else
                tracer.Visible = false
            end
        else
            tracer.Visible = false
        end
    end
end)

ESPTab:AddToggle({Name = "Enable ESP", Default = false, Flag = "ESPEnabled", Save = true,
    Callback = function(v) ESPEnabled = v end})

ESPTab:AddSection({Name = "ESP Info"})
ESPTab:AddToggle({Name = "Show Name",     Default = true, Flag = "ESPShowName",     Save = true, Callback = function(v) ESPSettings.ShowName     = v end})
ESPTab:AddToggle({Name = "Show Username", Default = true, Flag = "ESPShowUsername", Save = true, Callback = function(v) ESPSettings.ShowUsername = v end})
ESPTab:AddToggle({Name = "Show Health",   Default = true, Flag = "ESPShowHealth",   Save = true, Callback = function(v) ESPSettings.ShowHealth   = v end})
ESPTab:AddToggle({Name = "Show Distance", Default = true, Flag = "ESPShowDistance", Save = true, Callback = function(v) ESPSettings.ShowDistance = v end})
ESPTab:AddToggle({Name = "Show Team",     Default = true, Flag = "ESPShowTeam",     Save = true, Callback = function(v) ESPSettings.ShowTeam     = v end})
ESPTab:AddToggle({Name = "Show Wanted",   Default = true, Flag = "ESPShowWanted",   Save = true, Callback = function(v) ESPSettings.ShowWanted   = v end})
ESPTab:AddToggle({Name = "Team Check",    Default = false, Flag = "ESPTeamCheck",   Save = true, Callback = function(v) ESPSettings.TeamCheck    = v end})

ESPTab:AddSection({Name = "Tracer"})
ESPTab:AddToggle({Name = "Enable Tracer", Default = false, Flag = "ESPTracer", Save = true,
    Callback = function(v) ESPSettings.Tracer = v end})
ESPTab:AddSlider({Name = "Tracer Thickness", Min = 1, Max = 5, Increment = 1, Default = 1,
    ValueName = "px", Flag = "ESPTracerThickness", Save = true,
    Callback = function(v) ESPSettings.TracerThickness = v end})
ESPTab:AddSlider({Name = "Tracer Transparency", Min = 0, Max = 10, Increment = 1, Default = 5,
    ValueName = "/10", Flag = "ESPTracerTransparency", Save = true,
    Callback = function(v) ESPSettings.TracerTransparency = v / 10 end})

local PlayerTab = Window:MakeTab({Name = "Player", Icon = "rbxassetid://117259180607823", PremiumOnly = false})

local SpeedBoostEnabled, SpeedBoostValue = false, 0.5
PlayerTab:AddToggle({Name = "CFrame Speed (No WalkSpeed)", Default = false,
    Flag = "CFrameSpeedToggle", Save = true,
    Callback = function(v) SpeedBoostEnabled = v end})
PlayerTab:AddSlider({Name = "Speed Factor", Min = 1, Max = 50, Increment = 1, Default = 5,
    ValueName = "/10", Flag = "CFrameSpeedSlider", Save = true,
    Callback = function(v) SpeedBoostValue = v / 10 end})

RunService.Heartbeat:Connect(function()
    if not SpeedBoostEnabled or not LP.Character then return end
    local hum = LP.Character:FindFirstChild("Humanoid")
    local r   = LP.Character:FindFirstChild("HumanoidRootPart")
    if hum and r and hum.MoveDirection.Magnitude > 0 then
        r.CFrame = r.CFrame + (hum.MoveDirection * SpeedBoostValue)
    end
end)

local CFrameFlyEnabled, FlySpeed = false, 50
PlayerTab:AddToggle({Name = "CFrame Fly", Default = false, Flag = "CFrameFlyToggle", Save = true,
    Callback = function(v)
        CFrameFlyEnabled = v
        if LP.Character then
            local hum = LP.Character:FindFirstChild("Humanoid")
            if hum then hum.PlatformStand = v end
        end
    end})
PlayerTab:AddBind({Name = "CFrame Fly Key", Default = Enum.KeyCode.G, Hold = false,
    Flag = "CFrameFlyKeybind", Save = true,
    Callback = function()
        CFrameFlyEnabled = not CFrameFlyEnabled
        if LP.Character then
            local hum = LP.Character:FindFirstChild("Humanoid")
            if hum then hum.PlatformStand = CFrameFlyEnabled end
        end
    end})
PlayerTab:AddToggle({Name = "CFrame Fly Mobile UI", Default = false, Flag = "CFrameFlyMobileUIToggle", Save = true,
    Callback = function(v) CFrameFlyMobileUI.Enabled = v end})
PlayerTab:AddSlider({Name = "Fly Speed", Min = 10, Max = 300, Increment = 10, Default = 50,
    ValueName = "spd", Flag = "CFrameFlySpeed", Save = true,
    Callback = function(v) FlySpeed = v end})

RunService.RenderStepped:Connect(function(dt)
    if not CFrameFlyEnabled or not LP.Character then return end
    local r = LP.Character:FindFirstChild("HumanoidRootPart"); if not r then return end
    local cf = Camera.CFrame
    local m = Vector3.new()
    if UserInputService:IsKeyDown(Enum.KeyCode.W) or cfMobileW    then m = m + cf.LookVector  end
    if UserInputService:IsKeyDown(Enum.KeyCode.S) or cfMobileS    then m = m - cf.LookVector  end
    if UserInputService:IsKeyDown(Enum.KeyCode.A) or cfMobileA    then m = m - cf.RightVector end
    if UserInputService:IsKeyDown(Enum.KeyCode.D) or cfMobileD    then m = m + cf.RightVector end
    if UserInputService:IsKeyDown(Enum.KeyCode.Space) or cfMobileUp   then m = m + Vector3.new(0,1,0) end
    if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) or cfMobileDown then m = m - Vector3.new(0,1,0) end
    if m.Magnitude > 0 then r.CFrame = r.CFrame + (m.Unit * FlySpeed * dt) end
    r.AssemblyLinearVelocity  = Vector3.zero
    r.AssemblyAngularVelocity = Vector3.zero
end)

local NoFallEnabled = false
local NoFallConn
PlayerTab:AddToggle({Name = "Anti Fall Damage", Default = false, Flag = "NoFallToggle", Save = true,
    Callback = function(v)
        NoFallEnabled = v
        if v then
            NoFallConn = RunService.RenderStepped:Connect(function()
                if LP.Character then
                    local r = LP.Character:FindFirstChild("HumanoidRootPart")
                    if r and r.AssemblyLinearVelocity.Y < -30 then
                        local rp = RaycastParams.new()
                        rp.FilterDescendantsInstances = {LP.Character}
                        rp.FilterType = Enum.RaycastFilterType.Blacklist
                        if Workspace:Raycast(r.Position, Vector3.new(0,-20,0), rp) then
                            r.AssemblyLinearVelocity = Vector3.new(r.AssemblyLinearVelocity.X, 0, r.AssemblyLinearVelocity.Z)
                        end
                    end
                end
            end)
        elseif NoFallConn then
            NoFallConn:Disconnect(); NoFallConn = nil
        end
    end})

PlayerTab:AddSection({Name = "Spectate"})
local SpectateName = ""
PlayerTab:AddTextbox({Name = "Spectate Player Name", Default = "", TextDisappear = false,
    Callback = function(t) SpectateName = t end})
PlayerTab:AddButton({Name = "Start Spectating", Callback = function()
    for _, plr in pairs(Players:GetPlayers()) do
        if string.sub(string.lower(plr.Name), 1, #SpectateName) == string.lower(SpectateName)
        or string.sub(string.lower(plr.DisplayName), 1, #SpectateName) == string.lower(SpectateName) then
            if plr.Character and plr.Character:FindFirstChild("Humanoid") then
                Camera.CameraSubject = plr.Character.Humanoid
                notify("Spectating", "Watching " .. plr.Name, 2)
            end
        end
    end
end})
PlayerTab:AddButton({Name = "Stop Spectating", Callback = function()
    if LP.Character and LP.Character:FindFirstChild("Humanoid") then
        Camera.CameraSubject = LP.Character.Humanoid
    end
end})

PlayerTab:AddSection({Name = "Character Graphics"})
local VortexGhostEnabled, VortexGhostColor, VortexRainbowGhost = false, Color3.fromRGB(0,170,255), false
local SavedGhostColors = {}
local function ApplyGhost(c)
    if not LP.Character then return end
    for _, p in pairs(LP.Character:GetDescendants()) do
        if p:IsA("BasePart") and p.Transparency < 1 then
            if not SavedGhostColors[p] then SavedGhostColors[p] = {Color = p.Color, Material = p.Material} end
            p.Material = Enum.Material.ForceField
            p.Color    = c
        end
    end
end
local function RestoreGhost()
    if not LP.Character then return end
    for _, p in pairs(LP.Character:GetDescendants()) do
        if p:IsA("BasePart") and SavedGhostColors[p] then
            p.Material = SavedGhostColors[p].Material
            p.Color    = SavedGhostColors[p].Color
        end
    end
    SavedGhostColors = {}
end
PlayerTab:AddToggle({Name = "Player Ghost", Default = false, Flag = "VortexGhost", Save = true,
    Callback = function(v) VortexGhostEnabled = v; if v then ApplyGhost(VortexGhostColor) else RestoreGhost() end end})
PlayerTab:AddColorpicker({Name = "Ghost Color", Default = Color3.fromRGB(0,170,255),
    Flag = "VortexGhostColor", Save = true,
    Callback = function(v)
        VortexGhostColor = v
        if VortexGhostEnabled and not VortexRainbowGhost then ApplyGhost(v) end
    end})
PlayerTab:AddToggle({Name = "Rainbow Ghost", Default = false, Flag = "VortexRainbowGhost", Save = true,
    Callback = function(v) VortexRainbowGhost = v end})

RunService.RenderStepped:Connect(function()
    if VortexGhostEnabled and VortexRainbowGhost then
        ApplyGhost(Color3.fromHSV(tick() % 5 / 5, 1, 1))
    end
end)

local function GetOtherPlayers()
    local t = {"Random Player"}
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= LP then table.insert(t, p.Name) end
    end
    return t
end
local function CopySkin(target)
    if not target or not target.Character or not LP.Character then return end
    for _, v in pairs(LP.Character:GetChildren()) do
        if v:IsA("Shirt") or v:IsA("Pants") or v:IsA("ShirtGraphic") or v:IsA("Accessory") or v:IsA("BodyColors") then
            v:Destroy()
        end
    end
    for _, v in pairs(target.Character:GetChildren()) do
        if v:IsA("Shirt") or v:IsA("Pants") or v:IsA("ShirtGraphic") or v:IsA("BodyColors") then
            v:Clone().Parent = LP.Character
        elseif v:IsA("Accessory") then v:Clone().Parent = LP.Character
        elseif v.Name == "Head" and v:FindFirstChild("face") then
            local mh = LP.Character:FindFirstChild("Head")
            if mh then if mh:FindFirstChild("face") then mh.face:Destroy() end; v.face:Clone().Parent = mh end
        end
    end
end
PlayerTab:AddDropdown({Name = "Skinchanger", Options = GetOtherPlayers(),
    Default = "Random Player", Flag = "Skinchanger", Save = false,
    Callback = function(v)
        if v == "Random Player" then
            local plrs = Players:GetPlayers()
            local t = plrs[math.random(1, #plrs)]
            if t ~= LP then CopySkin(t) end
        else
            local t = Players:FindFirstChild(v); if t then CopySkin(t) end
        end
    end})

local VisualsTab = Window:MakeTab({Name = "Visuals", Icon = "rbxassetid://7035631382", PremiumOnly = false})

local FullbrightEnabled, NoFogEnabled = false, false
local TimeChangerEnabled, TimeChangerValue = false, 14

VisualsTab:AddToggle({Name = "Fullbright", Default = false, Flag = "FullbrightToggle", Save = true,
    Callback = function(v) FullbrightEnabled = v end})

VisualsTab:AddButton({Name = "FPS Boost (Anti Lag)", Callback = function()
    for _, v in pairs(workspace:GetDescendants()) do
        if v:IsA("BasePart") and v.Parent and not v.Parent:FindFirstChild("Humanoid") then
            v.Material = Enum.Material.SmoothPlastic
            v.CastShadow = false
        elseif v:IsA("Decal") or v:IsA("Texture") then
            v:Destroy()
        elseif v:IsA("ParticleEmitter") or v:IsA("Trail") then
            v.Enabled = false
        end
    end
    Lighting.GlobalShadows = false
    Lighting.FogEnd = 9e9
end})

VisualsTab:AddToggle({Name = "No Fog", Default = false, Flag = "NoFogToggle", Save = true,
    Callback = function(v) NoFogEnabled = v end})

VisualsTab:AddSlider({Name = "Field of View", Min = 30, Max = 120, Increment = 1, Default = 70,
    ValueName = "fov", Flag = "FOVSlider", Save = true,
    Callback = function(v) Camera.FieldOfView = v end})

VisualsTab:AddToggle({Name = "Enforce Time", Default = false, Flag = "TimeChangerToggle", Save = true,
    Callback = function(v) TimeChangerEnabled = v end})
VisualsTab:AddSlider({Name = "Clock Time", Min = 0, Max = 24, Increment = 1, Default = 14,
    ValueName = "h", Flag = "TimeSlider", Save = true,
    Callback = function(v) TimeChangerValue = v; Lighting.ClockTime = v end})

local FreecamEnabled, FreecamSpeed = false, 2
local FreecamKeys = {W=false,A=false,S=false,D=false,Q=false,E=false,Shift=false}
local FreecamMouseHeld = false

VisualsTab:AddToggle({Name = "Freecam", Default = false, Flag = "FreecamToggle", Save = false,
    Callback = function(v)
        FreecamEnabled = v
        if v then Camera.CameraType = Enum.CameraType.Scriptable
        else Camera.CameraType = Enum.CameraType.Custom; UserInputService.MouseBehavior = Enum.MouseBehavior.Default end
    end})
VisualsTab:AddSlider({Name = "Freecam Speed", Min = 1, Max = 100, Increment = 1, Default = 20,
    ValueName = "/10", Flag = "FreecamSpeed", Save = true,
    Callback = function(v) FreecamSpeed = v / 10 end})

UserInputService.InputBegan:Connect(function(input, gp)
    if not FreecamEnabled then return end
    if input.UserInputType == Enum.UserInputType.MouseButton2 then
        FreecamMouseHeld = true
        UserInputService.MouseBehavior = Enum.MouseBehavior.LockCurrentPosition
    end
    if gp then return end
    local kc = input.KeyCode
    if kc == Enum.KeyCode.W then FreecamKeys.W = true
    elseif kc == Enum.KeyCode.A then FreecamKeys.A = true
    elseif kc == Enum.KeyCode.S then FreecamKeys.S = true
    elseif kc == Enum.KeyCode.D then FreecamKeys.D = true
    elseif kc == Enum.KeyCode.Q then FreecamKeys.Q = true
    elseif kc == Enum.KeyCode.E then FreecamKeys.E = true
    elseif kc == Enum.KeyCode.LeftShift then FreecamKeys.Shift = true end
end)
UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton2 then
        FreecamMouseHeld = false
        UserInputService.MouseBehavior = Enum.MouseBehavior.Default
    end
    local kc = input.KeyCode
    if kc == Enum.KeyCode.W then FreecamKeys.W = false
    elseif kc == Enum.KeyCode.A then FreecamKeys.A = false
    elseif kc == Enum.KeyCode.S then FreecamKeys.S = false
    elseif kc == Enum.KeyCode.D then FreecamKeys.D = false
    elseif kc == Enum.KeyCode.Q then FreecamKeys.Q = false
    elseif kc == Enum.KeyCode.E then FreecamKeys.E = false
    elseif kc == Enum.KeyCode.LeftShift then FreecamKeys.Shift = false end
end)

RunService.RenderStepped:Connect(function(dt)
    if FullbrightEnabled then
        Lighting.Brightness = 2
        if not TimeChangerEnabled then Lighting.ClockTime = 14 end
        Lighting.FogEnd = 100000
        Lighting.GlobalShadows = false
        Lighting.OutdoorAmbient = Color3.fromRGB(128,128,128)
    end
    if NoFogEnabled then Lighting.FogEnd = 1000000 end
    if TimeChangerEnabled then Lighting.ClockTime = TimeChangerValue end

    if FreecamEnabled then
        local cf = Camera.CFrame
        if FreecamMouseHeld then
            local d = UserInputService:GetMouseDelta()
            local rot = Vector2.new(d.X, d.Y) * 0.5 * dt * 60
            cf = CFrame.new(cf.Position) * CFrame.Angles(0, math.rad(-rot.X), 0) * cf.Rotation * CFrame.Angles(math.rad(-rot.Y), 0, 0)
        end
        local m = Vector3.new()
        if FreecamKeys.W then m = m + Vector3.new(0,0,-1) end
        if FreecamKeys.S then m = m + Vector3.new(0,0,1) end
        if FreecamKeys.A then m = m + Vector3.new(-1,0,0) end
        if FreecamKeys.D then m = m + Vector3.new(1,0,0) end
        if FreecamKeys.Q then m = m + Vector3.new(0,-1,0) end
        if FreecamKeys.E then m = m + Vector3.new(0,1,0) end
        local sp = FreecamSpeed * 60 * dt
        if FreecamKeys.Shift then sp = sp * 5 end
        Camera.CFrame = cf * CFrame.new(m * sp)
    end
end)

VisualsTab:AddSection({Name = "Environment"})
local originalSky = Lighting:FindFirstChildOfClass("Sky")
VisualsTab:AddToggle({Name = "Remove Atmosphere", Default = false, Flag = "RemoveAtmosphere", Save = true,
    Callback = function(v)
        if v then
            local sky = Lighting:FindFirstChildOfClass("Sky")
            if sky then if not originalSky then originalSky = sky:Clone() end; sky:Destroy() end
        else
            if originalSky and not Lighting:FindFirstChildOfClass("Sky") then
                originalSky:Clone().Parent = Lighting
            end
        end
    end})

VisualsTab:AddToggle({Name = "Xray", Default = false, Flag = "XrayToggle", Save = true,
    Callback = function(v)
        for _, p in ipairs(workspace:GetDescendants()) do
            if p:IsA("BasePart") then
                local m = p:FindFirstAncestorWhichIsA("Model")
                if not (m and m:FindFirstChildOfClass("Humanoid")) then
                    p.LocalTransparencyModifier = v and 0.5 or 0
                end
            end
        end
    end})

local SkyPresets = {
    Standard = {SkyboxBk="rbxasset://textures/sky/sky512_bk.tex", SkyboxDn="rbxasset://textures/sky/sky512_dn.tex",
        SkyboxFt="rbxasset://textures/sky/sky512_ft.tex", SkyboxLf="rbxasset://textures/sky/sky512_lf.tex",
        SkyboxRt="rbxasset://textures/sky/sky512_rt.tex", SkyboxUp="rbxasset://textures/sky/sky512_up.tex"},
    Galaxy = {SkyboxBk="http://www.roblox.com/asset/?id=159454299", SkyboxDn="http://www.roblox.com/asset/?id=159454296",
        SkyboxFt="http://www.roblox.com/asset/?id=159454293", SkyboxLf="http://www.roblox.com/asset/?id=159454286",
        SkyboxRt="http://www.roblox.com/asset/?id=159454300", SkyboxUp="http://www.roblox.com/asset/?id=159454288"},
    Pink = {SkyboxBk="rbxassetid://12635309703", SkyboxDn="rbxassetid://12635311686",
        SkyboxFt="rbxassetid://12635312870", SkyboxLf="rbxassetid://12635313718",
        SkyboxRt="rbxassetid://12635315817", SkyboxUp="rbxassetid://12635316856"},
    Aesthetic = {SkyboxBk="rbxassetid://600830446", SkyboxDn="rbxassetid://600831635",
        SkyboxFt="rbxassetid://600832720", SkyboxLf="rbxassetid://600886090",
        SkyboxRt="rbxassetid://600833862", SkyboxUp="rbxassetid://600835177"}
}
VisualsTab:AddDropdown({Name = "Change Sky", Options = {"Standard","Galaxy","Pink","Aesthetic"},
    Default = "Standard", Flag = "ChangeSky", Save = true,
    Callback = function(v)
        local sky = Lighting:FindFirstChildOfClass("Sky")
        if not sky then sky = Instance.new("Sky"); sky.Parent = Lighting end
        local props = SkyPresets[v]
        if props then for k, val in pairs(props) do sky[k] = val end end
    end})

local MiscTab = Window:MakeTab({Name = "Misc", Icon = "rbxassetid://16149179345", PremiumOnly = false})

MiscTab:AddButton({Name = "Server Hop", Callback = function()
    local Http = game:GetService("HttpService")
    local TPS  = game:GetService("TeleportService")
    local _place = game.PlaceId
    local Api = "https://games.roblox.com/v1/games/".._place.."/servers/Public?sortOrder=Desc&limit=100"
    local function listServers(cursor)
        local raw = game:HttpGet(Api .. ((cursor and "&cursor="..cursor) or ""))
        return Http:JSONDecode(raw)
    end
    local nextCursor
    repeat
        local s = listServers(nextCursor)
        for _, v in pairs(s.data) do
            if v.playing < v.maxPlayers and v.id ~= game.JobId then
                TPS:TeleportToPlaceInstance(_place, v.id, LP); return
            end
        end
        nextCursor = s.nextPageCursor
    until not nextCursor
end})

MiscTab:AddButton({Name = "Rejoin Server", Callback = function()
    game:GetService("TeleportService"):TeleportToPlaceInstance(game.PlaceId, game.JobId, LP)
end})

MiscTab:AddSection({Name = "Safety"})
MiscTab:AddButton({Name = "Self Revive", Callback = function()
    local VIM = game:GetService("VirtualInputManager")
    local function isDead()
        if LP.Character then
            local h = LP.Character:FindFirstChildOfClass("Humanoid")
            if h then return h.Health <= 24 end
        end
        return false
    end
    local function goHospitalAndSit()
        local char = LP.Character or LP.CharacterAdded:Wait()
        char:MoveTo(Vector3.new(-107.427, 7.648, 1073.643))
        task.wait(1)
        local b = workspace:FindFirstChild("Buildings"); if not b then return end
        local hosp = b:FindFirstChild("Hospital"); if not hosp then return end
        local bed = hosp:FindFirstChild("HospitalBed"); if not bed then return end
        local seat = bed:FindFirstChild("Seat"); if not seat then return end
        char:MoveTo(seat.Position + Vector3.new(0, 2, 0))
        task.wait(0.7)
        local h = char:FindFirstChildOfClass("Humanoid")
        if h then seat:Sit(h) end
    end
    if isDead() then
        local startCF = LP.Character and LP.Character.PrimaryPart and LP.Character.PrimaryPart.CFrame or nil
        ensurePlayerInVehicle(); task.wait(0.5)
        teleportToLocation(CFrame.new(-89.70, 5.88, 1055.77), function()
            task.wait(1)
            LP.Character:MoveTo(Vector3.new(-107.427, 7.648, 1073.643))
            task.wait(0.5)
            goHospitalAndSit()
            task.spawn(function()
                local h = LP.Character:FindFirstChildOfClass("Humanoid")
                while h and h.Health <= 27 do
                    task.wait(1)
                    h = LP.Character and LP.Character:FindFirstChildOfClass("Humanoid")
                end
                VIM:SendKeyEvent(true,  Enum.KeyCode.Space, false, game); task.wait(0.1)
                VIM:SendKeyEvent(false, Enum.KeyCode.Space, false, game); task.wait(0.5)
                ensurePlayerInVehicle(); task.wait(0.5)
                if startCF then teleportToLocation(startCF) end
            end)
        end)
    else
        notify("Self Revive", "You are not dead.", 3)
    end
end})

local ConfigTab = Window:MakeTab({Name = "Configs", Icon = "rbxassetid://7733964640", PremiumOnly = false})

ConfigTab:AddSection({Name = "Manage Configs"})

local CurrentConfigName = "default"
local CurrentShareID    = ""

ConfigTab:AddTextbox({
    Name = "Config Name",
    Default = "default",
    TextDisappear = false,
    Callback = function(v) CurrentConfigName = (v ~= "" and v) or "default" end
})

ConfigTab:AddButton({Name = "Save Config", Callback = function()
    local ok, err = pcall(function() Library:CreateConfig(CurrentConfigName) end)
    if ok then notify("Configs", "Saved '" .. CurrentConfigName .. "'", 3)
    else notify("Configs", "Save failed: " .. tostring(err), 4) end
end})

ConfigTab:AddButton({Name = "Load Config", Callback = function()
    LoadingConfig = true
    local ok, err = pcall(function() Library:UseConfig(CurrentConfigName) end)
    task.delay(1, function() LoadingConfig = false end)
    if ok then notify("Configs", "Loaded '" .. CurrentConfigName .. "'", 3)
    else notify("Configs", "Load failed: " .. tostring(err), 4) end
end})

ConfigTab:AddButton({Name = "Delete Config", Callback = function()
    local ok, err = pcall(function() Library:DeleteConfig(CurrentConfigName) end)
    if ok then notify("Configs", "Deleted '" .. CurrentConfigName .. "'", 3)
    else notify("Configs", "Delete failed: " .. tostring(err), 4) end
end})

ConfigTab:AddButton({Name = "Set as Auto-Load", Callback = function()
    local ok, err = pcall(function() Library:AutoLoadConfig(CurrentConfigName) end)
    if ok then notify("Configs", "Auto-Load set to '" .. CurrentConfigName .. "'", 3)
    else notify("Configs", "Auto-Load failed: " .. tostring(err), 4) end
end})

ConfigTab:AddButton({Name = "Clear Auto-Load", Callback = function()
    local ok, err = pcall(function() Library:UnAutoLoadConfig() end)
    if ok then notify("Configs", "Auto-Load cleared", 3)
    else notify("Configs", "Clear failed: " .. tostring(err), 4) end
end})

ConfigTab:AddButton({Name = "List Configs (print)", Callback = function()
    local ok, list = pcall(function() return Library:ListConfigs() end)
    if ok and type(list) == "table" then
        notify("Configs", "Found " .. #list .. " config(s) - check console", 3)
        print("[Xeioa] Saved Configs:")
        for _, name in ipairs(list) do print(" - " .. tostring(name)) end
    else
        notify("Configs", "List failed", 3)
    end
end})

ConfigTab:AddSection({Name = "Share Configs"})

ConfigTab:AddButton({Name = "Copy Share ID", Callback = function()
    local ok, id = pcall(function() return Library:ShareConfigId(CurrentConfigName) end)
    if ok and id then
        CurrentShareID = id
        pcall(function() if setclipboard then setclipboard(tostring(id)) end end)
        notify("Configs", "Share ID copied: " .. tostring(id), 5)
    else
        notify("Configs", "Could not get share ID", 3)
    end
end})

ConfigTab:AddTextbox({
    Name = "Paste Share ID",
    Default = "",
    TextDisappear = false,
    Callback = function(v) CurrentShareID = v end
})

ConfigTab:AddButton({Name = "Use Share ID", Callback = function()
    if CurrentShareID == "" then notify("Configs", "Paste a Share ID first", 3); return end
    LoadingConfig = true
    local ok, err = pcall(function() Library:UseConfigId(CurrentShareID) end)
    task.delay(1, function() LoadingConfig = false end)
    if ok then notify("Configs", "Loaded shared config", 3)
    else notify("Configs", "Use failed: " .. tostring(err), 4) end
end})

ConfigTab:AddButton({Name = "Save Share ID Locally", Callback = function()
    if CurrentShareID == "" then notify("Configs", "Paste a Share ID first", 3); return end
    local ok, err = pcall(function() Library:SaveConfigId(CurrentConfigName, CurrentShareID) end)
    if ok then notify("Configs", "Saved share ID as '" .. CurrentConfigName .. "'", 3)
    else notify("Configs", "Save failed: " .. tostring(err), 4) end
end})

ConfigTab:AddSection({Name = "Danger Zone"})

ConfigTab:AddButton({Name = "Destroy UI", Callback = function()
    local ok = pcall(function() Library:Destroy() end)
    if not ok then notify("Configs", "Destroy failed", 3) end
end})

local SettingsTab = Window:MakeTab({Name = "Settings", Icon = "rbxassetid://3610239960", PremiumOnly = false})
SettingsTab:AddSection({Name = "Appearance"})
pcall(function() SettingsTab:AddThemePicker() end)
pcall(function() SettingsTab:AddFontPicker() end)
SettingsTab:AddSection({Name = "Info"})
SettingsTab:AddLabel("Emergency Hamburg | Xeioa Hub")
SettingsTab:AddParagraph("Themes", "Default, Light, Blurple, Red, Ocean, Forest, Gold")
SettingsTab:AddParagraph("Fonts",  "Gotham, GothamBold, FredokaOne, SourceSans, Roboto, RobotoMono, Nunito, Ubuntu, Cartoon, Code")

task.delay(1, function() UI_READY = true end)

Library:MakeNotification({
    Name    = "Welcome!",
    Content = "Emergency Hamburg | Xeioa Hub V2 loaded.",
    Image   = "rbxassetid://4384403532",
    Time    = 6
})
