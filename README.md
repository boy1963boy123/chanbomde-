-- SERVICES
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local VirtualUser = game:GetService("VirtualUser")
local HttpService = game:GetService("HttpService")
local TeleportService = game:GetService("TeleportService")
local TweenService = game:GetService("TweenService")

local plr = Players.LocalPlayer

-- CLEAN GUI
pcall(function()
	local old = plr.PlayerGui:FindFirstChild("chanbomde")
	if old then old:Destroy() end
end)

------------------------------------------------
-- DATA SYSTEM
------------------------------------------------
local DATA = {
	{"FARM CHEST",{
		{"Boss Chest 1",-275,4,-90},
		{"Boss Chest 2",-850,4,-580},
		{"Boss Chest 3",-1500,4,-140},
		{"Boss Chest 4",1900.64,4,220.69}
	}},
	{"MUA DO",{
		{"Mua Bang Gac",200.49,164.64,585.60}
	}},
	{"FARM TIEN",{
		{"Farm 1",170.60,161.30,659.53},
		{"Farm 2",240.78,161.59,591.39}
	}}
}

------------------------------------------------
-- SETTINGS
------------------------------------------------
local AntiAFK = true

------------------------------------------------
-- CORE FUNCTIONS
------------------------------------------------
local function TP(x,y,z)
	local char = plr.Character or plr.CharacterAdded:Wait()
	char:PivotTo(CFrame.new(x,y,z))
end

local function serverHop()
	local url = "https://games.roblox.com/v1/games/"..game.PlaceId.."/servers/Public?limit=50"

	local ok, res = pcall(function()
		return HttpService:JSONDecode(game:HttpGet(url))
	end)

	if ok and res and res.data then
		for _,v in pairs(res.data) do
			if v.playing < v.maxPlayers and v.id ~= game.JobId then
				TeleportService:TeleportToPlaceInstance(game.PlaceId, v.id, plr)
				break
			end
		end
	end
end

------------------------------------------------
-- ANTI AFK
------------------------------------------------
plr.Idled:Connect(function()
	if not AntiAFK then return end
	VirtualUser:Button2Down(Vector2.new(), workspace.CurrentCamera.CFrame)
	task.wait(1)
	VirtualUser:Button2Up(Vector2.new(), workspace.CurrentCamera.CFrame)
end)

------------------------------------------------
-- UI SYSTEM
------------------------------------------------
local gui = Instance.new("ScreenGui", plr.PlayerGui)
gui.Name = "chanbomde"
gui.ResetOnSpawn = false

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.fromOffset(280,350)
frame.Position = UDim2.new(0.05,0,0.2,0)
frame.BackgroundColor3 = Color3.fromRGB(25,25,25)
frame.Active = true
frame.Draggable = true
Instance.new("UICorner", frame)

local stroke = Instance.new("UIStroke", frame)
stroke.Thickness = 3

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1,0,0,35)
title.BackgroundTransparency = 1
title.Text = "chanbomde"
title.TextScaled = true
title.Font = Enum.Font.GothamBold

------------------------------------------------
-- SCROLL MENU
------------------------------------------------
local scroll = Instance.new("ScrollingFrame", frame)
scroll.Size = UDim2.new(1,-10,1,-65)
scroll.Position = UDim2.new(0,5,0,40)
scroll.BackgroundTransparency = 1
scroll.ScrollBarThickness = 6

Instance.new("UIListLayout", scroll).Padding = UDim.new(0,5)

-- BUILD BUTTONS
for _,cat in ipairs(DATA) do
	local label = Instance.new("TextLabel", scroll)
	label.Size = UDim2.new(1,0,0,30)
	label.BackgroundTransparency = 1
	label.Text = "=== "..cat[1].." ==="
	label.TextColor3 = Color3.fromRGB(255,255,0)

	for _,v in ipairs(cat[2]) do
		local btn = Instance.new("TextButton", scroll)
		btn.Size = UDim2.new(1,-5,0,35)
		btn.Text = v[1]
		Instance.new("UICorner", btn)

		btn.MouseButton1Click:Connect(function()
			TP(v[2],v[3],v[4])
		end)
	end
end

------------------------------------------------
-- SYSTEM BUTTONS
------------------------------------------------
local antiBtn = Instance.new("TextButton", scroll)
antiBtn.Size = UDim2.new(1,-5,0,35)
antiBtn.Text = "ANTI AFK: ON"
Instance.new("UICorner", antiBtn)

antiBtn.MouseButton1Click:Connect(function()
	AntiAFK = not AntiAFK
	antiBtn.Text = AntiAFK and "ANTI AFK: ON" or "ANTI AFK: OFF"
end)

local hopBtn = Instance.new("TextButton", scroll)
hopBtn.Size = UDim2.new(1,-5,0,35)
hopBtn.Text = "SERVER HOP"
Instance.new("UICorner", hopBtn)

hopBtn.MouseButton1Click:Connect(serverHop)

------------------------------------------------
-- RAINBOW UI
------------------------------------------------
RunService.RenderStepped:Connect(function()
	local c = Color3.fromHSV((tick()%5)/5,1,1)
	stroke.Color = c
	title.TextColor3 = c
end)

------------------------------------------------
-- RUNNING TEXT
------------------------------------------------
local textGui = Instance.new("ScreenGui", plr.PlayerGui)

local text = Instance.new("TextLabel", textGui)
text.Size = UDim2.new(0,300,0,40)
text.Position = UDim2.new(1,0,1,-40)
text.BackgroundTransparency = 0.3
text.BackgroundColor3 = Color3.fromRGB(0,0,0)
text.TextColor3 = Color3.fromRGB(255,255,255)
text.TextScaled = true
text.Font = Enum.Font.GothamBold
text.Text = "Tui đang update thêm..."

task.spawn(function()
	while true do
		local tween = TweenService:Create(
			text,
			TweenInfo.new(10),
			{Position = text.Position - UDim2.new(0,400,0,0)}
		)

		tween:Play()
		tween.Completed:Wait()
		text.Position = UDim2.new(1,0,1,-40)
	end
end)
