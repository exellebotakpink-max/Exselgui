-- AUTO WALK RECORDER FINAL
-- Save + Load + Auto Loop + Minimize + Face Back
-- By Exel

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local HttpService = game:GetService("HttpService")

local plr = Players.LocalPlayer
local char = plr.Character or plr.CharacterAdded:Wait()
local hum = char:WaitForChild("Humanoid")
local hrp = char:WaitForChild("HumanoidRootPart")

--------------------------------------------------
-- STATE
--------------------------------------------------
local recording = false
local playing = false
local speedMul = 2
local autoLoop = false
local faceBack = false
local minimized = false
local data = {}

--------------------------------------------------
-- FILE SYSTEM
--------------------------------------------------
local FOLDER = "ExelRecorder"
if writefile and not isfolder(FOLDER) then
	makefolder(FOLDER)
end

local function encodeCF(cf)
	return {cf:GetComponents()}
end

local function decodeCF(t)
	return CFrame.new(unpack(t))
end

local function saveRecording(name)
	if not writefile or name == "" then return end
	local pack = {}
	for i,v in ipairs(data) do
		pack[i] = {cf = encodeCF(v.cf), jump = v.jump}
	end
	writefile(FOLDER.."/"..name..".json", HttpService:JSONEncode(pack))
end

local function loadRecording(name)
	if not readfile then return end
	local path = FOLDER.."/"..name..".json"
	if not isfile(path) then return end
	local raw = HttpService:JSONDecode(readfile(path))
	data = {}
	for i,v in ipairs(raw) do
		data[i] = {cf = decodeCF(v.cf), jump = v.jump}
	end
end

--------------------------------------------------
-- GUI
--------------------------------------------------
local gui = Instance.new("ScreenGui", plr.PlayerGui)
gui.ResetOnSpawn = false

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0,210,0,340)
frame.Position = UDim2.new(0,20,0.32,0)
frame.BackgroundColor3 = Color3.fromRGB(90,0,150)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true

local function btn(text,y)
	local b = Instance.new("TextButton", frame)
	b.Size = UDim2.new(1,-20,0,28)
	b.Position = UDim2.new(0,10,0,y)
	b.Text = text
	b.BackgroundColor3 = Color3.fromRGB(170,80,255)
	b.TextColor3 = Color3.new(1,1,1)
	b.Font = Enum.Font.SourceSansBold
	b.TextSize = 13
	b.BorderSizePixel = 0
	return b
end

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1,0,0,28)
title.Text = "EXEL RECORDER"
title.BackgroundTransparency = 1
title.TextColor3 = Color3.new(1,1,1)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 14

local nameBox = Instance.new("TextBox", frame)
nameBox.Size = UDim2.new(1,-20,0,26)
nameBox.Position = UDim2.new(0,10,0,35)
nameBox.PlaceholderText = "Recording Name"
nameBox.Text = ""
nameBox.BackgroundColor3 = Color3.fromRGB(120,40,200)
nameBox.TextColor3 = Color3.new(1,1,1)
nameBox.BorderSizePixel = 0

local recordBtn = btn("RECORD",70)
local stopBtn   = btn("STOP",102)
local playBtn   = btn("PLAY",134)
local speedBtn  = btn("SPEED : 2x",166)
local loopBtn   = btn("AUTO LOOP : OFF",198)
local faceBtn   = btn("FACE BACK : OFF",230)
local saveBtn   = btn("SAVE",262)
local loadBtn   = btn("LOAD",294)

-- MINIMIZE
local miniBtn = Instance.new("TextButton", frame)
miniBtn.Size = UDim2.new(0,28,0,28)
miniBtn.Position = UDim2.new(1,-30,0,0)
miniBtn.Text = "-"
miniBtn.BackgroundColor3 = Color3.fromRGB(60,0,120)
miniBtn.TextColor3 = Color3.new(1,1,1)
miniBtn.BorderSizePixel = 0

--------------------------------------------------
-- BUTTON LOGIC
--------------------------------------------------
speedBtn.MouseButton1Click:Connect(function()
	speedMul += 1
	if speedMul > 20 then speedMul = 1 end
	speedBtn.Text = "SPEED : "..speedMul.."x"
end)

loopBtn.MouseButton1Click:Connect(function()
	autoLoop = not autoLoop
	loopBtn.Text = "AUTO LOOP : "..(autoLoop and "ON" or "OFF")
end)

faceBtn.MouseButton1Click:Connect(function()
	faceBack = not faceBack
	faceBtn.Text = "FACE BACK : "..(faceBack and "ON" or "OFF")
end)

miniBtn.MouseButton1Click:Connect(function()
	minimized = not minimized
	for _,v in ipairs(frame:GetChildren()) do
		if v ~= miniBtn then
			v.Visible = not minimized
		end
	end
	miniBtn.Text = minimized and "+" or "-"
	frame.Size = minimized and UDim2.new(0,60,0,40) or UDim2.new(0,210,0,340)
end)

recordBtn.MouseButton1Click:Connect(function()
	data = {}
	recording = true
end)

stopBtn.MouseButton1Click:Connect(function()
	recording = false
	playing = false
end)

saveBtn.MouseButton1Click:Connect(function()
	saveRecording(nameBox.Text)
end)

loadBtn.MouseButton1Click:Connect(function()
	loadRecording(nameBox.Text)
end)

--------------------------------------------------
-- RECORD
--------------------------------------------------
RunService.RenderStepped:Connect(function()
	if recording then
		table.insert(data,{
			cf = hrp.CFrame,
			jump = hum:GetState() == Enum.HumanoidStateType.Jumping
		})
	end
end)

--------------------------------------------------
-- PLAY
--------------------------------------------------
local function playRecording()
	if #data == 0 then return end
	playing = true

	hrp.CFrame = data[1].cf
	task.wait(0.15)

	hum.WalkSpeed = 16 * speedMul
	hum.AutoRotate = false

	for i = 1, #data do
		if not playing then break end

		local cf = data[i].cf
		if faceBack then
			cf = cf * CFrame.Angles(0, math.rad(180), 0)
		end

		hrp.CFrame = cf
		hum:Move(cf.LookVector, true)

		if data[i].jump then
			hum:ChangeState(Enum.HumanoidStateType.Jumping)
		end

		RunService.RenderStepped:Wait()
	end

	hum:Move(Vector3.zero)
	hum.AutoRotate = true
	playing = false

	if autoLoop then
		task.wait(0.3)
		playRecording()
	end
end

playBtn.MouseButton1Click:Connect(function()
	if not playing then
		playRecording()
	end
end)

print("Exel Recorder Loaded Successfully")
