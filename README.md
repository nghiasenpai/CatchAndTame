-- ==========================================
-- SCRIPT [⚓] Nghia Senpai hub (FIXED SAFARI ISLAND)
-- ==========================================

local CoreGui = game:GetService("CoreGui")
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TeleportService = game:GetService("TeleportService")
local HttpService = game:GetService("HttpService")
local VirtualInputManager = game:GetService("VirtualInputManager")
local Lighting = game:GetService("Lighting")
local SoundService = game:GetService("SoundService")
local TweenService = game:GetService("TweenService")
local MarketplaceService = game:GetService("MarketplaceService")

local LocalPlayer = Players.LocalPlayer

-- Tốc độ tăng 200% (3x)
local SPEED_MULTIPLIER = 3.0

-- ==========================================
-- KIỂM TRA GAME HỖ TRỢ
-- ==========================================
local function isSupportedGame()
    local gameName = ""
    local success, info = pcall(function()
        return MarketplaceService:GetProductInfo(game.PlaceId)
    end)
    
    if success and info then
        gameName = info.Name:lower()
    end
    
    local wsName = workspace.Name:lower()
    
    local supportedKeywords = {
        "catch", "fish", "tame", "pet", "monster", "ocean", "sea", 
        "island", "survival", "deep", "stealing", "collect", "creature",
        "animal", "beast", "hunt", "capture", "train", "aqua", "marine"
    }
    
    for _, kw in ipairs(supportedKeywords) do
        if gameName:find(kw) then
            return true
        end
        if wsName:find(kw) then
            return true
        end
    end
    
    return false
end

if not isSupportedGame() then
    local warningGui = Instance.new("ScreenGui")
    warningGui.Name = "NghiaSenpai_NotSupport"
    warningGui.ResetOnSpawn = false
    warningGui.Parent = CoreGui
    
    local frame = Instance.new("Frame", warningGui)
    frame.Size = UDim2.new(0, 350, 0, 150)
    frame.Position = UDim2.new(0.5, -175, 0.5, -75)
    frame.BackgroundColor3 = Color3.fromRGB(30, 30, 35)
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 8)
    
    local stroke = Instance.new("UIStroke", frame)
    stroke.Color = Color3.fromRGB(255, 50, 50)
    stroke.Thickness = 2
    
    local text = Instance.new("TextLabel", frame)
    text.Size = UDim2.new(1, -20, 1, -20)
    text.Position = UDim2.new(0, 10, 0, 10)
    text.BackgroundTransparency = 1
    text.Text = "❌ NghiaSenPai not support!\n\nScript chỉ hỗ trợ các game Bắt Thú, Thuần Hóa, Câu Cá!\nVui lòng sử dụng đúng game."
    text.TextColor3 = Color3.fromRGB(255, 100, 100)
    text.Font = Enum.Font.GothamBold
    text.TextSize = 14
    text.TextWrapped = true
    
    task.wait(5)
    warningGui:Destroy()
    return
end

-- Container an toàn
local parentContainer = nil
local successCore = pcall(function()
	return CoreGui
end)

if successCore and CoreGui then
	parentContainer = CoreGui
else
	parentContainer = LocalPlayer:WaitForChild("PlayerGui")
end

-- Xóa UI cũ nếu có tránh bị trùng lặp
if parentContainer:FindFirstChild("CatchHubFixedUI_FinalAntiSink") then
	parentContainer.CatchHubFixedUI_FinalAntiSink:Destroy()
end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "CatchHubFixedUI_FinalAntiSink"
ScreenGui.ResetOnSpawn = false
ScreenGui.DisplayOrder = 2147483647
ScreenGui.IgnoreGuiInset = true
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = parentContainer

-- Main Frame
local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 300, 0, 700)
MainFrame.Position = UDim2.new(0.5, -150, 0.5, -350)
MainFrame.BackgroundColor3 = Color3.fromRGB(24, 24, 28)
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Visible = true
MainFrame.ZIndex = 10

Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 8)
local MainStroke = Instance.new("UIStroke", MainFrame)
MainStroke.Color = Color3.fromRGB(0, 220, 130)
MainStroke.Thickness = 1.5

-- Title Bar
local TitleBar = Instance.new("Frame", MainFrame)
TitleBar.Size = UDim2.new(1, 0, 0, 35)
TitleBar.BackgroundColor3 = Color3.fromRGB(32, 32, 38)
Instance.new("UICorner", TitleBar).CornerRadius = UDim.new(0, 8)

local TitleText = Instance.new("TextLabel", TitleBar)
TitleText.Size = UDim2.new(1, -40, 1, 0)
TitleText.Position = UDim2.new(0, 10, 0, 0)
TitleText.BackgroundTransparency = 1
TitleText.Text = "⚓ Nghia Senpai Hub ⚓"
TitleText.TextColor3 = Color3.fromRGB(0, 220, 130)
TitleText.Font = Enum.Font.GothamBold
TitleText.TextSize = 10
TitleText.TextXAlignment = Enum.TextXAlignment.Left

local CloseBtn = Instance.new("TextButton", TitleBar)
CloseBtn.Size = UDim2.new(0, 24, 0, 24)
CloseBtn.Position = UDim2.new(1, -28, 0, 5)
CloseBtn.BackgroundColor3 = Color3.fromRGB(220, 50, 50)
CloseBtn.Text = "X"
CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.TextSize = 12
Instance.new("UICorner", CloseBtn).CornerRadius = UDim.new(0, 4)

CloseBtn.MouseButton1Click:Connect(function() 
	MainFrame.Visible = false 
end)

-- Bật tắt menu bằng Ctrl
UserInputService.InputBegan:Connect(function(input, gpe)
	if not gpe and (input.KeyCode == Enum.KeyCode.LeftControl or input.KeyCode == Enum.KeyCode.RightControl) then
		MainFrame.Visible = not MainFrame.Visible
	end
end)

-- Container
local Container = Instance.new("ScrollingFrame", MainFrame)
Container.Size = UDim2.new(1, -12, 1, -45)
Container.Position = UDim2.new(0, 6, 0, 40)
Container.BackgroundTransparency = 1
Container.ScrollBarThickness = 3
Container.AutomaticCanvasSize = Enum.AutomaticSize.Y
Container.ScrollingDirection = Enum.ScrollingDirection.Y
Container.CanvasSize = UDim2.new(0, 0, 0, 0)

local UIListLayout = Instance.new("UIListLayout", Container)
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
UIListLayout.Padding = UDim.new(0, 6)

local function AddButton(name, color, callback)
	local btn = Instance.new("TextButton", Container)
	btn.Size = UDim2.new(1, 0, 0, 32)
	btn.BackgroundColor3 = color or Color3.fromRGB(45, 45, 55)
	btn.Text = name
	btn.TextColor3 = Color3.fromRGB(255, 255, 255)
	btn.Font = Enum.Font.GothamBold
	btn.TextSize = 11
	Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
	btn.MouseButton1Click:Connect(callback)
	return btn
end

local function AddToggle(name, callback)
	local btn = Instance.new("TextButton", Container)
	btn.Size = UDim2.new(1, 0, 0, 32)
	btn.BackgroundColor3 = Color3.fromRGB(35, 35, 42)
	btn.Text = name .. " : [OFF]"
	btn.TextColor3 = Color3.fromRGB(180, 180, 180)
	btn.Font = Enum.Font.GothamSemibold
	btn.TextSize = 11
	Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 6)
	
	local enabled = false
	local function updateState(newState)
		enabled = newState
		btn.Text = name .. " : " .. (enabled and "[ON]" or "[OFF]")
		btn.BackgroundColor3 = enabled and Color3.fromRGB(0, 140, 70) or Color3.fromRGB(35, 35, 42)
		btn.TextColor3 = enabled and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(180, 180, 180)
		callback(enabled)
	end

	btn.MouseButton1Click:Connect(function() updateState(not enabled) end)
	return { SetState = updateState, GetState = function() return enabled end }
end

local function AddInput(labelTitle, defaultVal, callback)
	local frame = Instance.new("Frame", Container)
	frame.Size = UDim2.new(1, 0, 0, 32)
	frame.BackgroundColor3 = Color3.fromRGB(32, 32, 38)
	Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 6)

	local label = Instance.new("TextLabel", frame)
	label.Size = UDim2.new(0.65, 0, 1, 0)
	label.Position = UDim2.new(0, 8, 0, 0)
	label.BackgroundTransparency = 1
	label.Text = labelTitle
	label.TextColor3 = Color3.fromRGB(200, 200, 200)
	label.Font = Enum.Font.GothamSemibold
	label.TextSize = 11
	label.TextXAlignment = Enum.TextXAlignment.Left

	local box = Instance.new("TextBox", frame)
	box.Size = UDim2.new(0.3, -6, 0.7, 0)
	box.Position = UDim2.new(0.7, 0, 0.15, 0)
	box.BackgroundColor3 = Color3.fromRGB(50, 50, 60)
	box.Text = tostring(defaultVal)
	box.TextColor3 = Color3.fromRGB(255, 255, 255)
	box.Font = Enum.Font.GothamBold
	box.TextSize = 11
	Instance.new("UICorner", box).CornerRadius = UDim.new(0, 4)

	box.FocusLost:Connect(function()
		local val = box.Text
		if tonumber(val) then
			callback(tonumber(val))
		else
			callback(val)
		end
	end)
end

-- ==========================================
-- 1. 🎵 NHẠC
-- ==========================================
local globalVolumeMultiplier = 5
local musicSound = nil

local function getFallbackSound()
	if musicSound and musicSound.Parent then
		return musicSound
	end
	
	musicSound = Instance.new("Sound")
	musicSound.Name = "NghiaSenpaiHub_Music"
	musicSound.SoundId = "rbxassetid://1837843115"
	musicSound.Looped = true
	musicSound.Volume = globalVolumeMultiplier
	musicSound.Parent = SoundService
	
	return musicSound
end

AddToggle("🎵 Phát Nhạc Chill", function(state)
	pcall(function()
		local sound = getFallbackSound()
		if state then 
			sound:Play() 
		else 
			sound:Stop() 
		end
	end)
end)

AddButton("🎵 Nhạc Hà Anh Tuấn - Tháng Tư(not)", Color3.fromRGB(200, 150, 100), function()
	pcall(function()
		if musicSound then
			musicSound:Stop()
			musicSound:Destroy()
			musicSound = nil
		end
		
		musicSound = Instance.new("Sound")
		musicSound.Name = "NghiaSenpaiHub_HaAnhTuan"
		musicSound.SoundId = "rbxassetid://1845557374"
		musicSound.Looped = true
		musicSound.Volume = globalVolumeMultiplier
		musicSound.Parent = SoundService
		musicSound:Play()
	end)
end)

AddInput("🔊 Tăng Âm Lượng:", 5, function(v)
	globalVolumeMultiplier = tonumber(v) or 5
	pcall(function()
		if musicSound then
			musicSound.Volume = globalVolumeMultiplier
		end
	end)
end)

-- ==========================================
-- 2. 🏄 ĐI TRÊN MẶT NƯỚC
-- ==========================================
local waterWalkEnabled = false
local antiSinkPart = nil
local antiSinkConn = nil

AddToggle("🏄 Đi Trên Mặt Nước", function(state)
	waterWalkEnabled = state
	
	if waterWalkEnabled then
		if not antiSinkPart then
			antiSinkPart = Instance.new("Part")
			antiSinkPart.Name = "NghiaSenpai_AntiSinkPlatform"
			antiSinkPart.Size = Vector3.new(30, 1, 30)
			antiSinkPart.Anchored = true
			antiSinkPart.CanCollide = true
			antiSinkPart.Transparency = 1 
			antiSinkPart.Parent = workspace
		end

		antiSinkConn = RunService.RenderStepped:Connect(function()
			pcall(function()
				local char = LocalPlayer.Character
				local root = char and (char:FindFirstChild("HumanoidRootPart") or char.PrimaryPart)
				
				if root and antiSinkPart then
					local currentY = root.Position.Y
					local waterY = workspace.Terrain:GetWaterHeight(root.Position)
					
					if waterY and waterY > -500 then
						antiSinkPart.Position = Vector3.new(root.Position.X, waterY, root.Position.Z)
					else
						antiSinkPart.Position = Vector3.new(root.Position.X, currentY - 3.2, root.Position.Z)
					end
				end
			end)
		end)
	else
		if antiSinkConn then antiSinkConn:Disconnect() antiSinkConn = nil end
		if antiSinkPart then antiSinkPart:Destroy() antiSinkPart = nil end
	end
end)

-- ==========================================
-- 3. 👑 ESP
-- ==========================================
local rareESP = false
local espEnabled = false
local espCache = {}
local espUpdateConn = nil

local function getRarityInfo(target)
	local fullStr = target.Name:lower()
	local parentName = target.Parent and target.Parent.Name:lower() or ""
	
	if fullStr:find("divine") or parentName:find("divine") then return "DIVINE", Color3.fromRGB(255, 215, 0) end
	if fullStr:find("mythic") or fullStr:find("mythical") or parentName:find("mythic") or parentName:find("mythical") then return "MYTHICAL", Color3.fromRGB(180, 50, 255) end
	if fullStr:find("boss") or parentName:find("boss") then return "BOSS", Color3.fromRGB(255, 30, 30) end
	if fullStr:find("secret") or parentName:find("secret") then return "SECRET", Color3.fromRGB(255, 0, 128) end
	if fullStr:find("legendary") or parentName:find("legendary") then return "LEGENDARY", Color3.fromRGB(255, 140, 0) end
	return nil, nil
end

local function applyIslandESP(item)
	if not item or item == LocalPlayer.Character or item:IsDescendantOf(LocalPlayer.Character) then return end
	if Players:GetPlayerFromCharacter(item) then return end
	if not (item:IsA("Model") or item:IsA("BasePart")) then return end

	local rootPart = item:IsA("Model") and (item.PrimaryPart or item:FindFirstChild("HumanoidRootPart") or item:FindFirstChildOfClass("BasePart")) or item
	if not rootPart then return end

	local rareType, rareColor = getRarityInfo(item)
	local isRare = rareType ~= nil
	local shouldShow = (isRare and rareESP) or (not isRare and espEnabled)
	
	if not shouldShow then return end

	if espCache[item] then return end

	local tagColor = rareColor or Color3.fromRGB(0, 255, 150)
	local tagTitle = isRare and ("[" .. rareType .. "]\n" .. item.Name) or item.Name

	local hl = Instance.new("Highlight")
	hl.FillColor = tagColor
	hl.OutlineColor = Color3.fromRGB(255, 255, 255)
	hl.FillTransparency = 0.4
	hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
	hl.Parent = item

	local bgui = Instance.new("BillboardGui")
	bgui.Size = UDim2.new(0, 200, 0, 40)
	bgui.AlwaysOnTop = true
	bgui.MaxDistance = 500
	bgui.Parent = rootPart

	local textLbl = Instance.new("TextLabel", bgui)
	textLbl.Size = UDim2.new(1, 0, 1, 0)
	textLbl.BackgroundTransparency = 1
	textLbl.Font = Enum.Font.GothamBold
	textLbl.TextSize = 12
	textLbl.TextColor3 = tagColor
	textLbl.TextStrokeTransparency = 0.2
	textLbl.Text = tagTitle

	espCache[item] = { Highlight = hl, Billboard = bgui }
end

local function startESPUpdate()
	if espUpdateConn then
		espUpdateConn:Disconnect()
	end
	
	espUpdateConn = RunService.Heartbeat:Connect(function()
		if rareESP or espEnabled then
			local models = {}
			for _, obj in ipairs(workspace:GetDescendants()) do
				if obj:IsA("Model") and #models < 100 then
					table.insert(models, obj)
				end
				if #models >= 100 then break end
			end
			
			for _, obj in ipairs(models) do
				applyIslandESP(obj)
			end
		end
	end)
end

AddToggle("👑 ESP BOSS - Divine & Mythical(not)", function(state)
	rareESP = state
	if state then
		startESPUpdate()
	elseif not espEnabled then
		if espUpdateConn then espUpdateConn:Disconnect() espUpdateConn = nil end
	end
end)

AddToggle("✨ ESP Thường", function(state)
	espEnabled = state
	if state then
		startESPUpdate()
	elseif not rareESP then
		if espUpdateConn then espUpdateConn:Disconnect() espUpdateConn = nil end
	end
end)

-- ==========================================
-- 4. 🖱️ AUTO CLICK (SIÊU NHANH)
-- ==========================================
local autoClicker = false
local autoClickInterval = 0.1

local function startAutoClick()
	task.spawn(function()
		while autoClicker do
			pcall(function()
				local cam = workspace.CurrentCamera
				if cam then
					local clickX = cam.ViewportSize.X * 0.85
					local clickY = cam.ViewportSize.Y * 0.5
					VirtualInputManager:SendMouseButtonEvent(clickX, clickY, 0, true, game, 0)
					VirtualInputManager:SendMouseButtonEvent(clickX, clickY, 0, false, game, 0)
				end
			end)
			task.wait(autoClickInterval)
		end
	end)
end

local autoClickToggle = AddToggle("🖱️ Auto Click (Ấn Y, Siêu Nhanh)", function(state)
	autoClicker = state
	if state then
		startAutoClick()
	end
end)

UserInputService.InputBegan:Connect(function(input, gpe)
	if not gpe and input.KeyCode == Enum.KeyCode.Y then
		autoClickToggle.SetState(not autoClickToggle.GetState())
	end
end)

-- ==========================================
-- 5. 🏃 TỐC ĐỘ & NHẢY CAO
-- ==========================================
local walkSpeedVal = 50 * SPEED_MULTIPLIER
local speedEnabled = false

local function applyWalkSpeed()
	local char = LocalPlayer.Character
	local hum = char and char:FindFirstChildOfClass("Humanoid")
	if hum then
		hum.WalkSpeed = speedEnabled and walkSpeedVal or 16
	end
end

AddToggle("🏃 Chạy Nhanh", function(state)
	speedEnabled = state
	applyWalkSpeed()
end)

AddInput("⚙️ Giá Trị Tốc Độ:", walkSpeedVal, function(v)
	walkSpeedVal = tonumber(v) or 150
	if speedEnabled then
		applyWalkSpeed()
	end
end)

LocalPlayer.CharacterAdded:Connect(function(newChar)
	newChar:WaitForChild("Humanoid")
	task.wait(0.5)
	if speedEnabled then
		applyWalkSpeed()
	end
end)

local jumpPowerVal = 120 * SPEED_MULTIPLIER
local jumpEnabled = false
local infiniteJumpConnection = nil

AddToggle("🦘 Nhảy Cao", function(state)
	jumpEnabled = state
	if jumpEnabled then
		if infiniteJumpConnection then infiniteJumpConnection:Disconnect() end
		infiniteJumpConnection = UserInputService.JumpRequest:Connect(function()
			if jumpEnabled then
				pcall(function()
					local root = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
					if root then
						root.AssemblyLinearVelocity = Vector3.new(root.AssemblyLinearVelocity.X, jumpPowerVal, root.AssemblyLinearVelocity.Z)
					end
				end)
			end
		end)
	else
		if infiniteJumpConnection then infiniteJumpConnection:Disconnect() infiniteJumpConnection = nil end
	end
end)

-- ==========================================
-- 6. 🚀 DASH & TELEPORT
-- ==========================================
local dashEnabled = false
local dashDistance = 30 * SPEED_MULTIPLIER
AddToggle("⚡ Lướt Nhanh Phím Q", function(state)
	dashEnabled = state
end)

UserInputService.InputBegan:Connect(function(input, gpe)
	if not gpe and dashEnabled and input.KeyCode == Enum.KeyCode.Q then
		pcall(function()
			local char = LocalPlayer.Character
			local root = char and char:FindFirstChild("HumanoidRootPart")
			local cam = workspace.CurrentCamera
			if root and cam then
				local lookVec = cam.CFrame.LookVector
				local flatLook = Vector3.new(lookVec.X, 0, lookVec.Z).Unit
				root.CFrame = root.CFrame + (flatLook * dashDistance)
			end
		end)
	end
end)

local tpKeyEnabled = false
local tpStepDist = 25 * SPEED_MULTIPLIER
AddToggle("🎯 Dịch Chuyển Phím T", function(state)
	tpKeyEnabled = state
end)

UserInputService.InputBegan:Connect(function(input, gpe)
	if not gpe and tpKeyEnabled and input.KeyCode == Enum.KeyCode.T then
		pcall(function()
			local char = LocalPlayer.Character
			local root = char and char:FindFirstChild("HumanoidRootPart")
			local cam = workspace.CurrentCamera
			if root and cam then
				root.CFrame = root.CFrame + (cam.CFrame.LookVector * tpStepDist)
			end
		end)
	end
end)

-- ==========================================
-- 7. 🏝️ BAY ĐẾN ĐẢO (ĐÃ FIX SAFARI ISLAND)
-- ==========================================
local islandFlySpeed = 200 * SPEED_MULTIPLIER
local isFlyingToIsland = false
local currentFlyTween = nil

-- Danh sách đảo với ExcludeNames mạnh hơn
local islandList = {
    {Name = "Bee Island", SearchNames = {"bee island", "beeisland", "bee"}, ExcludeNames = {"home", "spawn", "lobby", "hub", "main"}},
    {Name = "Cave Island", SearchNames = {"cave island", "caveisland", "cave"}, ExcludeNames = {"underground", "below", "hidden", "home", "spawn", "lobby", "hub", "main"}},
    {Name = "Safari Island(beta)", SearchNames = {"safari island", "safariisland", "safari"}, ExcludeNames = {"home", "spawn", "lobby", "hub", "main", "base", "start", "begin", "central"}},
    {Name = "Volcano Island", SearchNames = {"volcano island", "volcanoisland", "volcano"}, ExcludeNames = {"home", "spawn", "lobby", "hub", "main"}},
    {Name = "Forgotten Depths", SearchNames = {"forgotten depths", "forgottendepths", "forgotten"}, ExcludeNames = {"home", "spawn", "lobby", "hub", "main"}},
    {Name = "Lost Docks", SearchNames = {"lost docks", "lostdocks", "docks"}, ExcludeNames = {"home", "spawn", "lobby", "hub", "main"}},
    {Name = "Sunken Island", SearchNames = {"sunken island", "sunkenisland", "sunken"}, ExcludeNames = {"home", "spawn", "lobby", "hub", "main"}},
    {Name = "Dragon Island(not)", SearchNames = {"dragon island", "dragonisland", "dragon"}, ExcludeNames = {"underground", "cave", "dungeon", "below", "under", "nest", "egg", "home", "spawn", "lobby", "hub", "main"}},
}

local function findIslandPosition(islandInfo)
    local excludeNames = islandInfo.ExcludeNames or {}
    local homePosition = nil
    
    -- Tìm vị trí home trước để loại bỏ
    for _, obj in ipairs(workspace:GetChildren()) do
        if obj:IsA("Model") or obj:IsA("BasePart") then
            local objName = obj.Name:lower()
            if objName:find("home") or objName:find("spawn") or objName:find("lobby") or objName:find("hub") or objName:find("main") then
                homePosition = obj:IsA("Model") and (obj.PrimaryPart and obj.PrimaryPart.Position or obj:GetPivot().Position) or obj.Position
                break
            end
        end
    end
    
    -- Tìm trong workspace children
    for _, obj in ipairs(workspace:GetChildren()) do
        if obj:IsA("Model") or obj:IsA("BasePart") then
            local objName = obj.Name:lower()
            local shouldExclude = false
            
            for _, excludeName in ipairs(excludeNames) do
                if objName:find(excludeName) then
                    shouldExclude = true
                    break
                end
            end
            
            if not shouldExclude then
                for _, searchName in ipairs(islandInfo.SearchNames) do
                    if objName:find(searchName) then
                        local pos = obj:IsA("Model") and (obj.PrimaryPart and obj.PrimaryPart.Position or obj:GetPivot().Position) or obj.Position
                        
                        -- Kiểm tra không phải vị trí home
                        if pos.Y > 5 and (not homePosition or (pos - homePosition).Magnitude > 50) then
                            return pos
                        end
                    end
                end
            end
        end
    end
    
    -- Tìm trong descendants
    for _, obj in ipairs(workspace:GetDescendants()) do
        if (obj:IsA("Model") or obj:IsA("BasePart")) and obj.Parent then
            local objName = obj.Name:lower()
            local parentName = obj.Parent.Name:lower()
            local shouldExclude = false
            
            for _, excludeName in ipairs(excludeNames) do
                if objName:find(excludeName) or parentName:find(excludeName) then
                    shouldExclude = true
                    break
                end
            end
            
            if not shouldExclude then
                for _, searchName in ipairs(islandInfo.SearchNames) do
                    if objName:find(searchName) or parentName:find(searchName) then
                        local pos = obj:IsA("Model") and (obj.PrimaryPart and obj.PrimaryPart.Position or obj:GetPivot().Position) or obj.Position
                        
                        -- Kiểm tra không phải vị trí home
                        if pos.Y > 5 and (not homePosition or (pos - homePosition).Magnitude > 50) then
                            return pos
                        end
                    end
                end
            end
        end
    end
    
    return nil
end

local function stopFlying()
    isFlyingToIsland = false
    
    if currentFlyTween then
        currentFlyTween:Cancel()
        currentFlyTween = nil
    end
    
    local root = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if root then
        root.AssemblyLinearVelocity = Vector3.new(0, 0, 0)
    end
    
    local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if hum then
        hum.PlatformStand = false
    end
end

local function flyToPosition(targetPosition)
    local char = LocalPlayer.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    
    if not root or not hum then return end
    
    stopFlying()
    
    isFlyingToIsland = true
    hum.PlatformStand = true
    
    local targetY = targetPosition.Y + 50
    local destination = Vector3.new(targetPosition.X, targetY, targetPosition.Z)
    
    local distance = (destination - root.Position).Magnitude
    local duration = math.clamp(distance / islandFlySpeed, 0.3, 3)
    
    local tweenInfo = TweenInfo.new(duration, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
    currentFlyTween = TweenService:Create(root, tweenInfo, {CFrame = CFrame.new(destination)})
    currentFlyTween:Play()
    
    currentFlyTween.Completed:Connect(function()
        if isFlyingToIsland then
            local landInfo = TweenInfo.new(0.5, Enum.EasingStyle.Quad, Enum.EasingDirection.In)
            local landTween = TweenService:Create(root, landInfo, {CFrame = CFrame.new(targetPosition + Vector3.new(0, 3, 0))})
            
            currentFlyTween = landTween
            landTween:Play()
            
            landTween.Completed:Connect(function()
                if isFlyingToIsland then
                    stopFlying()
                end
            end)
        end
    end)
end

local islandTitle = Instance.new("TextLabel", Container)
islandTitle.Size = UDim2.new(1, 0, 0, 25)
islandTitle.BackgroundTransparency = 1
islandTitle.Text = "🏝️ BAY ĐẾN ĐẢO (Siêu Nhanh):"
islandTitle.TextColor3 = Color3.fromRGB(0, 220, 130)
islandTitle.Font = Enum.Font.GothamBold
islandTitle.TextSize = 12
islandTitle.TextXAlignment = Enum.TextXAlignment.Left

for _, island in ipairs(islandList) do
    AddButton("🚁 " .. island.Name, Color3.fromRGB(0, 150, 130), function()
        pcall(function()
            local targetPos = findIslandPosition(island)
            if targetPos then
                print("[NghiaSenpai] Bay đến " .. island.Name .. " tại vị trí: " .. tostring(targetPos))
                flyToPosition(targetPos)
            else
                print("[NghiaSenpai] Không tìm thấy: " .. island.Name)
            end
        end)
    end)
end

AddButton("🛑 Dừng Bay", Color3.fromRGB(200, 50, 50), function()
    stopFlying()
end)

-- ==========================================
-- 8. 👫 TELEPORT ĐẾN NGƯỜI CHƠI
-- ==========================================
local selectedTargetPlayer = nil

local dropdownFrame = Instance.new("Frame", Container)
dropdownFrame.Size = UDim2.new(1, 0, 0, 32)
dropdownFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 42)
Instance.new("UICorner", dropdownFrame).CornerRadius = UDim.new(0, 6)

local dropdownBtn = Instance.new("TextButton", dropdownFrame)
dropdownBtn.Size = UDim2.new(1, 0, 1, 0)
dropdownBtn.BackgroundTransparency = 1
dropdownBtn.Text = "👤 Chọn Người Chơi"
dropdownBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
dropdownBtn.Font = Enum.Font.GothamSemibold
dropdownBtn.TextSize = 11

local listContainer = Instance.new("ScrollingFrame", ScreenGui)
listContainer.Size = UDim2.new(0, 280, 0, 0)
listContainer.Position = UDim2.new(0.5, -140, 0.5, -200)
listContainer.BackgroundColor3 = Color3.fromRGB(28, 28, 35)
listContainer.BorderSizePixel = 0
listContainer.Visible = false
listContainer.ScrollBarThickness = 3
listContainer.AutomaticCanvasSize = Enum.AutomaticSize.Y
listContainer.ZIndex = 1000
Instance.new("UICorner", listContainer).CornerRadius = UDim.new(0, 6)

local listLayout = Instance.new("UIListLayout", listContainer)
listLayout.SortOrder = Enum.SortOrder.LayoutOrder
listLayout.Padding = UDim.new(0, 3)

local function updatePlayerList()
    for _, child in ipairs(listContainer:GetChildren()) do
        if child:IsA("TextButton") then
            child:Destroy()
        end
    end

    local count = 0
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer then
            count = count + 1
            local pBtn = Instance.new("TextButton", listContainer)
            pBtn.Size = UDim2.new(1, 0, 0, 28)
            pBtn.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
            pBtn.Text = " ➔ " .. p.Name
            pBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
            pBtn.Font = Enum.Font.Gotham
            pBtn.TextSize = 11
            pBtn.TextXAlignment = Enum.TextXAlignment.Left
            pBtn.ZIndex = 1001
            Instance.new("UICorner", pBtn).CornerRadius = UDim.new(0, 4)

            pBtn.MouseButton1Click:Connect(function()
                selectedTargetPlayer = p
                dropdownBtn.Text = "👤 Đã chọn: " .. p.Name
                dropdownBtn.TextColor3 = Color3.fromRGB(0, 255, 150)
                listContainer.Visible = false
                listContainer.Size = UDim2.new(0, 280, 0, 0)
            end)
        end
    end
    
    listContainer.Size = UDim2.new(0, 280, 0, math.clamp(count * 31, 0, 300))
end

dropdownBtn.MouseButton1Click:Connect(function()
    if listContainer.Visible then
        listContainer.Visible = false
        listContainer.Size = UDim2.new(0, 280, 0, 0)
    else
        updatePlayerList()
        listContainer.Visible = true
    end
end)

AddButton("🚀 Dịch Chuyển Đến Người", Color3.fromRGB(0, 140, 220), function()
    pcall(function()
        if selectedTargetPlayer and selectedTargetPlayer.Character and selectedTargetPlayer.Character:FindFirstChild("HumanoidRootPart") then
            local myChar = LocalPlayer.Character
            local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
            if myRoot then
                myRoot.CFrame = selectedTargetPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, 3)
            end
        end
    end)
end)

-- ==========================================
-- 9. 🕊️ BAY SIÊU MƯỢT
-- ==========================================
local flySpeed = 150 * SPEED_MULTIPLIER
local isFlying = false
local flyConn
local flyBodyVelocity = nil
local flyBodyGyro = nil

AddToggle("🕊️ Chức Năng Bay (Siêu Mượt)", function(state)
    isFlying = state
    local root = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if not root or not hum then return end

    if state then
        hum.PlatformStand = true
        
        flyBodyVelocity = Instance.new("BodyVelocity", root)
        flyBodyVelocity.Name = "OptFlyBV"
        flyBodyVelocity.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
        flyBodyVelocity.Velocity = Vector3.new(0, 0, 0)
        
        flyBodyGyro = Instance.new("BodyGyro", root)
        flyBodyGyro.Name = "OptFlyBG"
        flyBodyGyro.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
        flyBodyGyro.D = 1000
        flyBodyGyro.P = 50000
        flyBodyGyro.CFrame = root.CFrame

        flyConn = RunService.RenderStepped:Connect(function()
            if not isFlying then return end
            
            local cam = workspace.CurrentCamera
            local moveDir = Vector3.new()
            
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveDir = moveDir + cam.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDir = moveDir - cam.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDir = moveDir + cam.CFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDir = moveDir - cam.CFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then moveDir = moveDir + Vector3.new(0, 1, 0) end
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then moveDir = moveDir - Vector3.new(0, 1, 0) end
            
            flyBodyGyro.CFrame = cam.CFrame
            
            if moveDir.Magnitude > 0 then
                flyBodyVelocity.Velocity = moveDir.Unit * flySpeed
            else
                flyBodyVelocity.Velocity = Vector3.new(0, 0, 0)
            end
        end)
    else
        if flyConn then flyConn:Disconnect() end
        if flyBodyVelocity then flyBodyVelocity:Destroy() end
        if flyBodyGyro then flyBodyGyro:Destroy() end
        hum.PlatformStand = false
    end
end)
AddInput("⚙️ Tốc Độ Bay:", flySpeed, function(v) flySpeed = tonumber(v) or 450 end)

-- Noclip
local noclip = false
local noclipConnection = nil

AddToggle("👻 Xuyên Tường (Noclip)", function(state)
    noclip = state
    if noclipConnection then
        noclipConnection:Disconnect()
        noclipConnection = nil
    end
    
    if noclip then
        noclipConnection = RunService.Stepped:Connect(function()
            if noclip and LocalPlayer.Character then
                for _, part in ipairs(LocalPlayer.Character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end
        end)
    end
end)

AddToggle("🛡️ Chống Rơi Vực", function(state)
    _G.AntiVoid = state
    task.spawn(function()
        while _G.AntiVoid do
            pcall(function()
                local root = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                if root and root.Position.Y < -40 then
                    root.CFrame = root.CFrame + Vector3.new(0, 120, 0)
                    root.AssemblyLinearVelocity = Vector3.new(0,0,0)
                end
            end)
            task.wait(0.2)
        end
    end)
end)

AddButton("⚡ Tăng FPS", Color3.fromRGB(160, 40, 200), function()
    pcall(function()
        setfpscap(9999)
    end)
end)

AddButton("🔄 Rejoin Server", Color3.fromRGB(40, 120, 80), function()
    TeleportService:TeleportToPlaceInstance(game.PlaceId, game.JobId, LocalPlayer)
end)

AddButton("🔄 Reset Nhân Vật", Color3.fromRGB(180, 90, 0), function()
    pcall(function()
        local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
        if hum then
            hum.Health = 0
        end
    end)
end)

AddButton("⚠️ Tự Kick", Color3.fromRGB(180, 40, 40), function()
    LocalPlayer:Kick("\n[⚓ Nghia Senpai Hub ⚓]\nĐã thoát game thành công!")
end)

-- Nút mở lại menu
local ToggleButton = Instance.new("TextButton", ScreenGui)
ToggleButton.Name = "HubOpenBtn"
ToggleButton.Size = UDim2.new(0, 50, 0, 50)
ToggleButton.Position = UDim2.new(0, 15, 0.4, 0)
ToggleButton.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
ToggleButton.Text = "HUB"
ToggleButton.TextColor3 = Color3.fromRGB(0, 220, 130)
ToggleButton.Font = Enum.Font.GothamBold
ToggleButton.TextSize = 12
ToggleButton.Active = true
ToggleButton.Draggable = true
ToggleButton.ZIndex = 100

Instance.new("UICorner", ToggleButton).CornerRadius = UDim.new(1, 0)
local tStroke = Instance.new("UIStroke", ToggleButton)
tStroke.Color = Color3.fromRGB(0, 220, 130)
tStroke.Thickness = 2.5

ToggleButton.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
end)

print("[Nghia Senpai Hub] Script đã tải thành công!")
