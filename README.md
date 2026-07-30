-- ==========================================
-- SCRIPT [⚓] Nghia Senpai hub (FIXED RUNSPEED BUG)
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

local LocalPlayer = Players.LocalPlayer

-- ==========================================
-- 🔒 BẢO MẬT: KHÓA CHẶN NGOÀI GAME BẮT & THUẦN HÓA (FISHING / CATCH GAMES)
-- ==========================================
local allowedPlaceIds = {
    -- Thêm các PlaceId chính thức của game nếu muốn
}

local function isTargetGame()
    local success, info = pcall(function()
        return game:GetService("MarketplaceService"):GetProductInfo(game.PlaceId)
    end)
    
    local gameName = success and info and info.Name:lower() or ""
    local placeId = game.PlaceId
    
    for _, id in ipairs(allowedPlaceIds) do
        if placeId == id then return true end
    end
    
    local keywords = {"catch", "fish", "tame", "pet", "monster", "ocean", "sea", "island", "survival", "deep", "stealing", "collect"}
    for _, kw in ipairs(keywords) do
        if gameName:find(kw) then return true end
    end
    
    local wsName = workspace.Name:lower()
    if wsName:find("fish") or wsName:find("catch") or wsName:find("ocean") or wsName:find("sea") then
        return true
    end
    
    return false
end

if not isTargetGame() then
    local warningGui = Instance.new("ScreenGui")
    warningGui.Name = "NghiaSenpai_BlockGui"
    warningGui.Parent = CoreGui
    
    local frame = Instance.new("Frame", warningGui)
    frame.Size = UDim2.new(0, 320, 0, 140)
    frame.Position = UDim2.new(0.5, -160, 0.5, -70)
    frame.BackgroundColor3 = Color3.fromRGB(30, 30, 35)
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 8)
    
    local stroke = Instance.new("UIStroke", frame)
    stroke.Color = Color3.fromRGB(255, 50, 50)
    stroke.Thickness = 2
    
    local text = Instance.new("TextLabel", frame)
    text.Size = UDim2.new(1, -20, 1, -20)
    text.Position = UDim2.new(0, 10, 0, 10)
    text.BackgroundTransparency = 1
    text.Text = "❌ [LỖI BẢO MẬT SCRIPT]\n\nNghia Senpai Hub chỉ hỗ trợ chạy trên các dòng game Bắt Thú, Câu Cá hoặc Thuần Hóa! Script sẽ tự hủy để bảo vệ bạn."
    text.TextColor3 = Color3.fromRGB(255, 100, 100)
    text.Font = Enum.Font.GothamBold
    text.TextSize = 12
    text.TextWrapped = true
    
    task.wait(4)
    warningGui:Destroy()
    error("Nghia Senpai Hub: Sai tựa game! Script đã bị khóa.")
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
ScreenGui.Parent = parentContainer

-- Main Frame
local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 280, 0, 600)
MainFrame.Position = UDim2.new(0.5, -140, 0.5, -300)
MainFrame.BackgroundColor3 = Color3.fromRGB(24, 24, 28)
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Visible = true

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
TitleText.Text = "⚓ Nghia Senpai Hub (Anti-Sink) ⚓"
TitleText.TextColor3 = Color3.fromRGB(0, 220, 130)
TitleText.Font = Enum.Font.GothamBold
TitleText.TextSize = 10
TitleText.TextXAlignment = Enum.TextXAlignment.Left

local CloseBtn = Instance.new("TextButton", TitleBar)
CloseBtn.Size = UDim2.new(0, 24, 0, 24)
CloseBtn.Position = UDim2.new(1, -28, 0, 5)
CloseBtn.BackgroundColor3 = Color3.fromRGB(220, 50, 50)
CloseBtn.Text = "✕"
CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseBtn.Font = Enum.Font.GothamBold
CloseBtn.TextSize = 12
Instance.new("UICorner", CloseBtn).CornerRadius = UDim.new(0, 4)

CloseBtn.MouseButton1Click:Connect(function() 
	MainFrame.Visible = false 
end)

UserInputService.InputBegan:Connect(function(input, gpe)
	if not gpe and input.KeyCode == Enum.KeyCode.LeftControl then
		MainFrame.Visible = not MainFrame.Visible
	end
end)

local Container = Instance.new("ScrollingFrame", MainFrame)
Container.Size = UDim2.new(1, -12, 1, -45)
Container.Position = UDim2.new(0, 6, 0, 40)
Container.BackgroundTransparency = 1
Container.ScrollBarThickness = 3
Container.AutomaticCanvasSize = Enum.AutomaticSize.Y

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
-- 1. 🎵 ÂM THANH & TĂNG ÂM LƯỢNG
-- ==========================================
local globalVolumeMultiplier = 5
local function getFallbackSound()
	local sound = SoundService:FindFirstChild("NghiaSenpaiHub_UltraMusic")
	if not sound then
		sound = Instance.new("Sound")
		sound.Name = "NghiaSenpaiHub_UltraMusic"
		sound.SoundId = "rbxassetid://9048375041"
		sound.Looped = true
		sound.Volume = globalVolumeMultiplier
		sound.Parent = SoundService
	end
	return sound
end

AddToggle("🎵 Phát Nhạc Chill Nền", function(state)
	pcall(function()
		local fallback = getFallbackSound()
		if state then fallback:Play() else fallback:Stop() end
	end)
end)

AddInput("🔊 Tăng Âm Lượng (vd: 10, 50)", 5, function(v)
	globalVolumeMultiplier = tonumber(v) or 5
	pcall(function()
		local fallback = getFallbackSound()
		fallback.Volume = globalVolumeMultiplier
	end)
end)

-- ==========================================
-- 2. 🏄 ĐI TRÊN MẶT NƯỚC (ANTI-SINK PRO)
-- ==========================================
local waterWalkEnabled = false
local antiSinkPart = nil
local antiSinkConn = nil

AddToggle("🏄 Đi Trên Mặt Nước (Anti-Sink Pro)", function(state)
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

		local fixedWaterY = nil

		antiSinkConn = RunService.RenderStepped:Connect(function()
			pcall(function()
				local char = LocalPlayer.Character
				local root = char and (char:FindFirstChild("HumanoidRootPart") or char.PrimaryPart)
				local hum = char and char:FindFirstChildOfClass("Humanoid")
				
				if root and antiSinkPart then
					local currentY = root.Position.Y
					if not fixedWaterY or math.abs(currentY - fixedWaterY) > 80 then
						local successWater = pcall(function()
							fixedWaterY = workspace.Terrain:GetWaterHeight(root.Position)
						end)
						if not successWater or not fixedWaterY or fixedWaterY == 0 or fixedWaterY < -500 then
							local raycastParams = RaycastParams.new()
							raycastParams.FilterType = Enum.RaycastFilterType.Exclude
							raycastParams.FilterDescendantsInstances = {char}
							local ray = workspace:Raycast(root.Position + Vector3.new(0, 5, 0), Vector3.new(0, -60, 0), raycastParams)
							if ray then
								fixedWaterY = ray.Position.Y
							else
								fixedWaterY = currentY - 3.2
							end
						end
					end

					antiSinkPart.Position = Vector3.new(root.Position.X, fixedWaterY, root.Position.Z)
					antiSinkPart.CanCollide = true

					if root.Position.Y <= fixedWaterY + 4 then
						if hum then hum:SetStateEnabled(Enum.HumanoidStateType.Swimming, false) end
					else
						if hum then hum:SetStateEnabled(Enum.HumanoidStateType.Swimming, true) end
					end
				end
			end)
		end)
	else
		fixedWaterY = nil
		if antiSinkConn then antiSinkConn:Disconnect() antiSinkConn = nil end
		if antiSinkPart then antiSinkPart:Destroy() antiSinkPart = nil end
		pcall(function()
			local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
			if hum then hum:SetStateEnabled(Enum.HumanoidStateType.Swimming, true) end
		end)
	end
end)

-- ==========================================
-- 3. 👑 ESP BOSS & THƯỜNG
-- ==========================================
local rareESP = false
local espEnabled = false
local islandMaxDistance = 500
local espCache = {}

local function getRarityInfo(target)
	local fullStr = target.Name:lower()
	if fullStr:find("divine") then return "DIVINE", Color3.fromRGB(255, 215, 0) end
	if fullStr:find("mythic") or fullStr:find("mythical") then return "MYTHICAL", Color3.fromRGB(180, 50, 255) end
	if fullStr:find("boss") then return "BOSS", Color3.fromRGB(255, 30, 30) end
	if fullStr:find("secret") then return "SECRET", Color3.fromRGB(255, 0, 128) end
	if fullStr:find("legendary") then return "LEGENDARY", Color3.fromRGB(255, 140, 0) end
	return nil, nil
end

local function clearModelESP(item)
	if espCache[item] then
		if espCache[item].Highlight then pcall(function() espCache[item].Highlight:Destroy() end) end
		if espCache[item].Billboard then pcall(function() espCache[item].Billboard:Destroy() end) end
		espCache[item] = nil
	end
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
	
	if not shouldShow then
		clearModelESP(item)
		return
	end

	if espCache[item] then return end

	local tagColor = rareColor or Color3.fromRGB(0, 255, 150)
	local tagTitle = isRare and ("[" .. rareType .. "]\n" .. item.Name) or item.Name

	local hl = Instance.new("Highlight")
	hl.FillColor = tagColor
	hl.OutlineColor = Color3.fromRGB(255, 255, 255)
	hl.FillTransparency = 0.4
	hl.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
	hl.Enabled = false
	hl.Parent = item

	local bgui = Instance.new("BillboardGui")
	bgui.Size = UDim2.new(0, 150, 0, 30)
	bgui.AlwaysOnTop = true
	bgui.MaxDistance = islandMaxDistance
	bgui.Enabled = false
	bgui.Parent = rootPart

	local textLbl = Instance.new("TextLabel", bgui)
	textLbl.Size = UDim2.new(1, 0, 1, 0)
	textLbl.BackgroundTransparency = 1
	textLbl.Font = Enum.Font.GothamBold
	textLbl.TextSize = 10
	textLbl.TextColor3 = tagColor
	textLbl.TextStrokeTransparency = 0.2

	task.spawn(function()
		while item and item.Parent do
			pcall(function()
				local myChar = LocalPlayer.Character
				local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
				if myRoot and rootPart then
					local dist = math.floor((rootPart.Position - myRoot.Position).Magnitude)
					if dist <= islandMaxDistance then
						hl.Enabled = true
						bgui.Enabled = true
						textLbl.Text = tagTitle .. " [" .. dist .. "m]"
					else
						hl.Enabled = false
						bgui.Enabled = false
					end
				end
			end)
			task.wait(0.5)
		end
		clearModelESP(item)
	end)

	espCache[item] = { Highlight = hl, Billboard = bgui }
end

AddToggle("👑 ESP BOSS - Divine & Mythical", function(state)
	rareESP = state
	for _, v in ipairs(workspace:GetDescendants()) do applyIslandESP(v) end
end)

AddToggle("✨ ESP Thường", function(state)
	espEnabled = state
	for _, v in ipairs(workspace:GetDescendants()) do applyIslandESP(v) end
end)

-- ==========================================
-- 4. 🖱️ AUTO CLICK PHÍM Y (ĐÃ FIX LỖI XUNG ĐỘT)
-- ==========================================
local autoClicker = false
local autoClickToggle = AddToggle("🖱️ Auto Click Phím Y", function(state)
	autoClicker = state
	if autoClicker then
		task.spawn(function()
			while autoClicker do
				pcall(function()
					local cam = workspace.CurrentCamera
					if cam then
						VirtualInputManager:SendMouseButtonEvent(cam.ViewportSize.X * 0.25, cam.ViewportSize.Y * 0.5, 0, true, game, 0)
						task.wait(0.01)
						VirtualInputManager:SendMouseButtonEvent(cam.ViewportSize.X * 0.25, cam.ViewportSize.Y * 0.5, 0, false, game, 0)
					end
				end)
				task.wait(0.1)
			end
		end)
	end
end)

UserInputService.InputBegan:Connect(function(input, gpe)
	if not gpe and input.KeyCode == Enum.KeyCode.Y then
		autoClickToggle.SetState(not autoClickToggle.GetState())
	end
end)

-- ==========================================
-- 5. 🏃 TỐC ĐỘ & NHẢY CAO (ĐÃ FIX AN TOÀN KHI HỒI SINH/ĐỔI TRẠNG THÁI)
-- ==========================================
local walkSpeedVal = 50
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
	walkSpeedVal = tonumber(v) or 50
	if speedEnabled then
		applyWalkSpeed()
	end
end)

-- Lắng nghe sự kiện nhân vật hồi sinh để tự động áp dụng lại tốc độ chạy
LocalPlayer.CharacterAdded:Connect(function(newChar)
	newChar:WaitForChild("Humanoid")
	task.wait(0.5)
	if speedEnabled then
		applyWalkSpeed()
	end
end)

local jumpPowerVal = 120
local jumpEnabled = false
local infiniteJumpConnection = nil

AddToggle("🦘 Nhảy Cao (Infinite Jump)", function(state)
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
AddInput("⚙️ Giá Trị Nhảy Cao:", jumpPowerVal, function(v) jumpPowerVal = tonumber(v) or 120 end)

-- ==========================================
-- 6. 🚀 HỖ TRỢ ĐI LẠI (LƠ LỬNG, DASH Q, TP T)
-- ==========================================
local lowGravityEnabled = false
local customGravity = 50
local gravityConn = nil

AddToggle("🎈 Nhảy Lơ Lửng (Đã Fix)", function(state)
	lowGravityEnabled = state
	local char = LocalPlayer.Character
	local root = char and char:FindFirstChild("HumanoidRootPart")
	
	if lowGravityEnabled then
		if root and not root:FindFirstChild("NghiaSenpai_FloatForce") then
			local att = Instance.new("Attachment", root)
			att.Name = "NghiaSenpai_FloatAtt"
			
			local vf = Instance.new("VectorForce", root)
			vf.Name = "NghiaSenpai_FloatForce"
			vf.Attachment0 = att
			vf.RelativeTo = Enum.ActuatorRelativeTo.World
			
			gravityConn = RunService.RenderStepped:Connect(function()
				pcall(function()
					if lowGravityEnabled and root and root.Parent then
						local mass = 0
						for _, p in ipairs(char:GetDescendants()) do
							if p:IsA("BasePart") then mass = mass + p.AssemblyMass end
						end
						vf.Force = Vector3.new(0, mass * (196.2 - customGravity), 0)
					end
				end)
			end)
		end
	else
		if gravityConn then gravityConn:Disconnect() gravityConn = nil end
		if root then
			if root:FindFirstChild("NghiaSenpai_FloatForce") then root.NghiaSenpai_FloatForce:Destroy() end
			if root:FindFirstChild("NghiaSenpai_FloatAtt") then root.NghiaSenpai_FloatAtt:Destroy() end
		end
	end
end)

AddInput("⚙️ Mức Độ Lơ Lửng (vd: 30, 80)", customGravity, function(v)
	customGravity = tonumber(v) or 50
end)

local dashEnabled = false
local dashDistance = 30
AddToggle("⚡ Bật Lướt Nhanh Phím Q (Dash)", function(state)
	dashEnabled = state
end)
AddInput("⚙️ Khoảng Cách Lướt Q:", dashDistance, function(v)
	dashDistance = tonumber(v) or 30
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
local tpStepDist = 25
AddToggle("🎯 Bật Dịch Chuyển Phím T (Forward TP)", function(state)
	tpKeyEnabled = state
end)
AddInput("⚙️ Khoảng Cách TP Phím T:", tpStepDist, function(v)
	tpStepDist = tonumber(v) or 25
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
-- 7. 👫 CHỌN NGƯỜI CHƠI ĐỂ TELEPORT
-- ==========================================
local selectedTargetPlayer = nil

local dropdownFrame = Instance.new("Frame", Container)
dropdownFrame.Size = UDim2.new(1, 0, 0, 32)
dropdownFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 42)
Instance.new("UICorner", dropdownFrame).CornerRadius = UDim.new(0, 6)

local dropdownBtn = Instance.new("TextButton", dropdownFrame)
dropdownBtn.Size = UDim2.new(1, 0, 1, 0)
dropdownBtn.BackgroundTransparency = 1
dropdownBtn.Text = "👤 Chọn Người Chơi: [ Chưa Chọn ]"
dropdownBtn.TextColor3 = Color3.fromRGB(200, 200, 200)
dropdownBtn.Font = Enum.Font.GothamSemibold
dropdownBtn.TextSize = 11

local listContainer = Instance.new("ScrollingFrame", Container)
listContainer.Size = UDim2.new(1, 0, 0, 0)
listContainer.BackgroundColor3 = Color3.fromRGB(28, 28, 35)
listContainer.BorderSizePixel = 0
listContainer.Visible = false
listContainer.ScrollBarThickness = 3
listContainer.AutomaticCanvasSize = Enum.AutomaticSize.Y
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
			pBtn.Text = " ➔ " .. p.Name .. " (" .. p.DisplayName .. ")"
			pBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
			pBtn.Font = Enum.Font.Gotham
			pBtn.TextSize = 10
			pBtn.TextXAlignment = Enum.TextXAlignment.Left
			Instance.new("UICorner", pBtn).CornerRadius = UDim.new(0, 4)

			pBtn.MouseButton1Click:Connect(function()
				selectedTargetPlayer = p
				dropdownBtn.Text = "👤 Đã chọn: " .. p.Name
				dropdownBtn.TextColor3 = Color3.fromRGB(0, 255, 150)
				listContainer.Visible = false
				listContainer.Size = UDim2.new(1, 0, 0, 0)
			end)
		end
	end
	
	listContainer.Size = UDim2.new(1, 0, 0, math.clamp(count * 31, 0, 120))
end

dropdownBtn.MouseButton1Click:Connect(function()
	if listContainer.Visible then
		listContainer.Visible = false
		listContainer.Size = UDim2.new(1, 0, 0, 0)
	else
		updatePlayerList()
		listContainer.Visible = true
	end
end)

AddButton("🚀 Dịch Chuyển Đến Người Đã Chọn", Color3.fromRGB(0, 140, 220), function()
	pcall(function()
		if selectedTargetPlayer and selectedTargetPlayer.Character and selectedTargetPlayer.Character:FindFirstChild("HumanoidRootPart") then
			local myChar = LocalPlayer.Character
			local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
			if myRoot then
				myRoot.CFrame = selectedTargetPlayer.Character.HumanoidRootPart.CFrame * CFrame.new(0, 0, 3)
			end
		else
			dropdownBtn.Text = "⚠️ Chưa chọn hoặc người chơi không hợp lệ!"
			dropdownBtn.TextColor3 = Color3.fromRGB(255, 80, 80)
		end
	end)
end)

-- ==========================================
-- 8. 🕊️ BAY, XUYÊN TƯỜNG, CHỐNG RƠI VỰC & TIỆN ÍCH KHÁC
-- ==========================================
local flySpeed = 60
local isFlying = false
local flyConn
AddToggle("🕊️ Chức Năng Bay (Fly)", function(state)
	isFlying = state
	local root = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
	local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
	if not root or not hum then return end

	if state then
		hum.PlatformStand = true
		local bv = Instance.new("BodyVelocity", root)
		bv.Name = "OptFlyBV"
		bv.MaxForce = Vector3.new(1e6, 1e6, 1e6)
		local bg = Instance.new("BodyGyro", root)
		bg.Name = "OptFlyBG"
		bg.MaxTorque = Vector3.new(1e6, 1e6, 1e6)

		flyConn = RunService.RenderStepped:Connect(function()
			if not isFlying then flyConn:Disconnect() return end
			local cam = workspace.CurrentCamera
			local moveDir = Vector3.new()
			if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveDir = moveDir + cam.CFrame.LookVector end
			if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDir = moveDir - cam.CFrame.LookVector end
			if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDir = moveDir + cam.CFrame.RightVector end
			if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDir = moveDir - cam.CFrame.RightVector end
			bg.CFrame = cam.CFrame
			bv.Velocity = moveDir.Magnitude > 0 and moveDir.Unit * flySpeed or Vector3.new(0,0,0)
		end)
	else
		if flyConn then flyConn:Disconnect() end
		if root:FindFirstChild("OptFlyBV") then root.OptFlyBV:Destroy() end
		if root:FindFirstChild("OptFlyBG") then root.OptFlyBG:Destroy() end
		hum.PlatformStand = false
	end
end)
AddInput("⚙️ Tốc Độ Bay:", flySpeed, function(v) flySpeed = tonumber(v) or 60 end)

local noclip = false
AddToggle("👻 Xuyên Tường (Noclip)", function(state) noclip = state end)
RunService.Stepped:Connect(function()
	if noclip and LocalPlayer.Character then
		for _, part in ipairs(LocalPlayer.Character:GetDescendants()) do
			if part:IsA("BasePart") then part.CanCollide = false end
		end
	end
end)

AddToggle("🛡️ Chống Rơi Vực (Anti Void)", function(state)
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
			task.wait(0.5)
		end
	end)
end)

AddButton("⚡ Tăng FPS Tối Đa (FPS Booster)", Color3.fromRGB(160, 40, 200), function()
	pcall(function()
		setfpscap(9999)
	end)
end)

AddButton("🔄 Vào Lại Server Này (Rejoin)", Color3.fromRGB(40, 120, 80), function()
	TeleportService:TeleportToPlaceInstance(game.PlaceId, game.JobId, LocalPlayer)
end)

AddButton("🔀 Đổi Server Khác (Server Hop)", Color3.fromRGB(40, 100, 180), function()
	pcall(function()
		local sf = HttpService:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/" .. game.PlaceId .. "/servers/Public?sortOrder=Asc&limit=100"))
		for _, s in ipairs(sf.data) do
			if s.playing < s.maxPlayers and s.id ~= game.JobId then
				TeleportService:TeleportToPlaceInstance(game.PlaceId, s.id, LocalPlayer)
				break
			end
		end
	end)
end)

AddButton("⚠️ Tự Kick Khỏi Game", Color3.fromRGB(180, 40, 40), function()
	LocalPlayer:Kick("\n[⚓ Nghia Senpai Hub ⚓]\nĐã thoát game thành công!")
end)

-- ==========================================
-- 9. 🔄 RESET NHÂN VẬT & 📉 GIẢM ĐỒ HỌA (Ở DƯỚI CÙNG)
-- ==========================================
AddButton("🔄 Reset Nhân Vật Ngay Lập Tức", Color3.fromRGB(180, 90, 0), function()
	pcall(function()
		local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
		if hum then
			hum.Health = 0
		end
	end)
end)

AddButton("📉 Giảm Đồ Họa Cực Mạnh (Ultra Potato)", Color3.fromRGB(200, 120, 20), function()
	pcall(function()
		Lighting.GlobalShadows = false
		Lighting.FogEnd = 9e9
		Lighting.Brightness = 0
		Lighting.ClockTime = 12
		
		for _, v in ipairs(Lighting:GetChildren()) do
			if v:IsA("PostEffect") or v:IsA("BloomEffect") or v:IsA("BlurEffect") or v:IsA("SunRaysEffect") or v:IsA("DepthOfFieldEffect") or v:IsA("ColorCorrectionEffect") or v:IsA("Atmosphere") then
				v:Destroy()
			end
		end

		for _, v in ipairs(workspace:GetDescendants()) do
			if v:IsA("ParticleEmitter") or v:IsA("Trail") or v:IsA("Smoke") or v:IsA("Fire") or v:IsA("Sparkles") or v:IsA("Beam") or v:IsA("Decal") or v:IsA("Texture") then
				v:Destroy()
			elseif v:IsA("BasePart") then
				v.Material = Enum.Material.SmoothPlastic
				v.Reflectance = 0
				v.CastShadow = false
			end
		end
	end)
end)

-- Nút mở lại menu nổi (Floating Toggle Button) cố định trên màn hình
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

Instance.new("UICorner", ToggleButton).CornerRadius = UDim.new(1, 0)
local tStroke = Instance.new("UIStroke", ToggleButton)
tStroke.Color = Color3.fromRGB(0, 220, 130)
tStroke.Thickness = 2.5

ToggleButton.MouseButton1Click:Connect(function()
	MainFrame.Visible = not MainFrame.Visible
end)
