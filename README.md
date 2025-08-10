print("[Ultimate Script] Iniciando...")

--[[ 🔐 Sistema de Key (válida por 1 dia/local) ]]
local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
local keyFileName = "mlhub_key.txt"

-- Função para gerar uma key aleatória
local function generateRandomKey()
	local chars = "ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
	local key = ""
	for i = 1, 8 do
		local randIndex = math.random(1, #chars)
		key = key .. chars:sub(randIndex, randIndex)
	end
	return key .. "_" .. os.date("%d%m")
end

-- Verificar se a key é válida
local function isValidKey(savedData)
	local today = os.date("%d/%m/%Y")
	if not savedData then return false end
	local decoded = HttpService:JSONDecode(savedData)
	return decoded.key and decoded.date == today
end

-- Tentar carregar key do arquivo
valid = false
if pcall(function() readfile(keyFileName) end) then
	local saved = readfile(keyFileName)
	valid = isValidKey(saved)
end

if not valid then
	-- Criar GUI de Key
	local keyGui = Instance.new("ScreenGui", playerGui)
	keyGui.Name = "KeySystem"
	keyGui.ResetOnSpawn = false

	local frame = Instance.new("Frame", keyGui)
	frame.Size = UDim2.new(0, 300, 0, 150)
	frame.Position = UDim2.new(0.5, -150, 0.5, -75)
	frame.BackgroundColor3 = Color3.fromRGB(25, 45, 25) -- verde escuro
	frame.BorderSizePixel = 0
	Instance.new("UICorner", frame)

	local title = Instance.new("TextLabel", frame)
	title.Text = "🔐 Insira sua Key"
	title.Size = UDim2.new(1, 0, 0, 30)
	title.Position = UDim2.new(0, 0, 0, 0)
	title.TextColor3 = Color3.new(1,1,1)
	title.BackgroundTransparency = 1
	title.Font = Enum.Font.GothamBold
	title.TextSize = 16

	local textBox = Instance.new("TextBox", frame)
	textBox.PlaceholderText = "Digite a key aqui..."
	textBox.Size = UDim2.new(0.9, 0, 0, 30)
	textBox.Position = UDim2.new(0.05, 0, 0.35, 0)
	textBox.Text = ""
	textBox.TextColor3 = Color3.new(1,1,1)
	textBox.BackgroundColor3 = Color3.fromRGB(40,60,40) -- verde médio
	textBox.Font = Enum.Font.Gotham
	textBox.TextSize = 14
	Instance.new("UICorner", textBox)

	local message = Instance.new("TextLabel", frame)
	message.Size = UDim2.new(1, 0, 0, 20)
	message.Position = UDim2.new(0, 0, 0.65, 0)
	message.BackgroundTransparency = 1
	message.Text = ""
	message.TextColor3 = Color3.new(1,0.2,0.2)
	message.Font = Enum.Font.Gotham
	message.TextSize = 14

	local confirmBtn = Instance.new("TextButton", frame)
	confirmBtn.Size = UDim2.new(0.4, 0, 0, 30)
	confirmBtn.Position = UDim2.new(0.05, 0, 0.8, 0)
	confirmBtn.Text = "✅ Confirmar"
	confirmBtn.BackgroundColor3 = Color3.fromRGB(0, 120, 0) -- verde forte
	confirmBtn.TextColor3 = Color3.new(1,1,1)
	confirmBtn.Font = Enum.Font.GothamBold
	confirmBtn.TextSize = 14
	Instance.new("UICorner", confirmBtn)

	local generateBtn = Instance.new("TextButton", frame)
	generateBtn.Size = UDim2.new(0.4, 0, 0, 30)
	generateBtn.Position = UDim2.new(0.55, 0, 0.8, 0)
	generateBtn.Text = "🎲 Gerar Key"
	generateBtn.BackgroundColor3 = Color3.fromRGB(30, 90, 30) -- verde escuro
	generateBtn.TextColor3 = Color3.new(1,1,1)
	generateBtn.Font = Enum.Font.GothamBold
	generateBtn.TextSize = 14
	Instance.new("UICorner", generateBtn)

	-- Gerar key aleatória
	local generatedKey = generateRandomKey()
	generateBtn.MouseButton1Click:Connect(function()
		textBox.Text = generatedKey
	end)

	-- Confirmar key
	confirmBtn.MouseButton1Click:Connect(function()
		local input = textBox.Text
		if input == generatedKey then
			local data = HttpService:JSONEncode({
				key = input,
				date = os.date("%d/%m/%Y")
			})
			writefile(keyFileName, data)
			keyGui:Destroy()
			print("[Tux Hub] ✅ Key verificada com sucesso!")
		else
			message.Text = "❌ Key incorreta. Gere uma válida!"
		end
	end)

	repeat task.wait() until not keyGui or not keyGui.Parent
end

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Workspace = game:GetService("Workspace")

local player = Players.LocalPlayer
while not player do task.wait() player = Players.LocalPlayer end

local toggles = { jump = false, god = false, flightToSpawn = false, enemyHighlight = false, petESP = false }

local jumpValue = 150
local initialPosition = nil
local isFlyingToSpawn = false

-- Listas de pets
local godPets = {
	"Cocofanto Elefanto", "Girafa Celestre", "Gattatino Neonino", "Matteo", "Tralalero Tralala",
	"Los Crocodillitos", "Espresso Signora", "Odin Din Din Dun", "Statutino Libertino", "Tukanno Bananno",
	"Trenostruzzo Turbo 3000", "Trippi Troppi Troppa Trippa", "Ballerino Lololo", "Los Tungtungtungcitos",
	"Piccione Macchina", "Tigroligre Frutonni"
}

local secretPets = {
	"La Vacca Saturno Saturnita", "Chimpanzini Spiderini", "Agarrini la Palini", "Los Tralaleritos",
	"Las Tralaleritas", "Las Vaquitas Saturnitas", "Graipuss Medussi", "Chicleteira Bicicleteira",
	"La Grande Combinasion", "Los Combinasionas", "Nuclearo Dinossauro", "Garama and Madundung",
	"Dragon Cannelloni", "Lucky Block Torrtuginni Dragonfrutini", "Pot Hotspot"
}

-- GUI
local gui = Instance.new("ScreenGui")
gui.Name = "UltimateFunctionalGUI"
gui.ResetOnSpawn = false

task.spawn(function()
	while not player:FindFirstChild("PlayerGui") do task.wait() end
	gui.Parent = player:FindFirstChild("PlayerGui")
end)

local icon = Instance.new("ImageButton")
icon.Size = UDim2.new(0, 50, 0, 50)
icon.Position = UDim2.new(0.02, 0, 0.4, 0)
icon.Image = "rbxassetid://84630240025384"
icon.BackgroundColor3 = Color3.fromRGB(60, 120, 70) -- verde escuro suave
icon.BackgroundTransparency = 0
icon.Active = true
icon.Parent = gui

local iconCorner = Instance.new("UICorner")
iconCorner.CornerRadius = UDim.new(1, 0)
iconCorner.Parent = icon

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 220, 0, 280) -- compacto
frame.Position = UDim2.new(0.1, 0, 0.35, 0) -- posição ajustada para mobile
frame.BackgroundColor3 = Color3.fromRGB(90, 130, 90) -- verde médio suave
frame.Visible = false
frame.Active = true
frame.Draggable = true
frame.Parent = gui

Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 16)

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 36)
title.Text = "TUX HUB"
title.BackgroundTransparency = 1
title.TextColor3 = Color3.fromRGB(180, 230, 180) -- verde claro
title.Font = Enum.Font.GothamBold
title.TextSize = 22
title.Parent = frame

icon.MouseButton1Click:Connect(function()
	frame.Visible = not frame.Visible
end)

-- Função de voo
local function flyToPositionSmooth(targetPos)
	local char = player.Character
	if not char then return end

	local root = char:FindFirstChild("HumanoidRootPart")
	local hum = char:FindFirstChildOfClass("Humanoid")
	if not root or not hum or not targetPos then return end

	isFlyingToSpawn = true
	toggles.flightToSpawn = true

	hum.BreakJointsOnDeath = false
	hum:SetStateEnabled(Enum.HumanoidStateType.Dead, false)
	hum.PlatformStand = false
	hum.AutoRotate = false
	hum.MaxHealth = math.huge
	hum.Health = math.huge

	local velocidade = 80
	local tolerancia = 1.5
	local arrived = false

	local protectConnection
	protectConnection = RunService.Heartbeat:Connect(function()
		if not isFlyingToSpawn or not char.Parent then
			protectConnection:Disconnect()
			return
		end
		pcall(function()
			hum.Health = math.huge
			hum.BreakJointsOnDeath = false
			hum.PlatformStand = false
			hum:SetStateEnabled(Enum.HumanoidStateType.Dead, false)
			if root.Velocity.Magnitude > 100 then
				root.Velocity = Vector3.zero
			end
		end)
	end)

	while not arrived and root and root.Parent do
		RunService.RenderStepped:Wait()
		if not toggles.flightToSpawn then break end
		local direction = (targetPos - root.Position)
		local distance = direction.Magnitude
		if distance <= tolerancia then arrived = true break end
		local moveDir = direction.Unit * math.min(velocidade * RunService.RenderStepped:Wait(), distance)
		root.CFrame = root.CFrame + moveDir
	end

	isFlyingToSpawn = false
	toggles.flightToSpawn = false
	hum.AutoRotate = true
	hum.PlatformStand = false
	hum:SetStateEnabled(Enum.HumanoidStateType.Dead, true)
	hum:ChangeState(Enum.HumanoidStateType.GettingUp)
end

local function flyForwardThenSpawn()
	local char = player.Character
	if not char then return end
	local root = char:FindFirstChild("HumanoidRootPart")
	if not root or not initialPosition then return end

	local cam = Workspace.CurrentCamera
	local forward = cam.CFrame.LookVector

	local forwardPos = root.Position + (forward * 50)

	flyToPositionSmooth(forwardPos)
	flyToPositionSmooth(initialPosition + Vector3.new(0,3,0))
end

-- Botões -- Função para criar botões no estilo verde compacto
local function makeToggleButton(text, y, toggleKey)
	local btn = Instance.new("TextButton", frame)
	btn.Size = UDim2.new(0.9, 0, 0, 35)
	btn.Position = UDim2.new(0.05, 0, 0, y)
	btn.BackgroundColor3 = Color3.fromRGB(90, 160, 90) -- verde médio
	btn.TextColor3 = Color3.fromRGB(220, 255, 220) -- verde claro
	btn.Font = Enum.Font.Gotham
	btn.TextSize = 16

	local function updateText()
		btn.Text = text .. (toggles[toggleKey] and " [ON]" or " [OFF]")
		btn.BackgroundColor3 = toggles[toggleKey] and Color3.fromRGB(60, 120, 60) or Color3.fromRGB(90, 160, 90)
	end

	btn.MouseButton1Click:Connect(function()
		toggles[toggleKey] = not toggles[toggleKey]
		updateText()

		if toggleKey == "flightToSpawn" and toggles.flightToSpawn then
			task.spawn(function()
				flyForwardThenSpawn()
				toggles.flightToSpawn = false
				updateText()
			end)
		end
	end)
-- Botão de 2 Dash para frente
local dashBtn = Instance.new("TextButton", frame)
dashBtn.Size = UDim2.new(0.9, 0, 0, 35)
dashBtn.Position = UDim2.new(0.05, 0, 0, 265)
dashBtn.BackgroundColor3 = Color3.fromRGB(100, 200, 100) -- verde claro
dashBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
dashBtn.Font = Enum.Font.Gotham
dashBtn.TextSize = 16
dashBtn.Text = "2x Dash"
dashBtn.MouseButton1Click:Connect(function()
	local char = player.Character
	local root = char and char:FindFirstChild("HumanoidRootPart")
	if not root then return end

	local cam = Workspace.CurrentCamera
	local forward = cam.CFrame.LookVector.Unit
	local dashDistance = 10
	local dashSpeed = 60

	for i = 1, 2 do
		local targetPos = root.Position + (forward * dashDistance)
		local direction = (targetPos - root.Position).Unit
		root.Velocity = direction * dashSpeed
		task.wait(0.1)
	end
end)

	updateText()
end

makeToggleButton("Super Pulo", 40, "jump")
makeToggleButton("God Mode", 85, "god")
makeToggleButton("Voar p/ Posição Inicial", 130, "flightToSpawn")
makeToggleButton("Esp Player", 175, "enemyHighlight")
makeToggleButton("ESP Brainrot", 220, "petESP")

-- God Mode
local function applyGod()
	local char = player.Character
	local hum = char and char:FindFirstChildOfClass("Humanoid")
	if not hum then return end

	if toggles.god then
		hum.MaxHealth = math.huge
		hum.Health = math.huge
		hum.BreakJointsOnDeath = false
	else
		hum.MaxHealth = 100
		if hum.Health > 100 then hum.Health = 100 end
		hum.BreakJointsOnDeath = true
	end
end

local function onCharacterAdded(char)
	task.wait(1)
	local hum = char:WaitForChild("Humanoid")
	local root = char:WaitForChild("HumanoidRootPart")
	initialPosition = root.Position
	applyGod()
end

player.CharacterAdded:Connect(onCharacterAdded)
if player.Character then onCharacterAdded(player.Character) end

-- ESP inimigos
local function updateEnemyHighlights()
	for _, p in ipairs(Players:GetPlayers()) do
		if p ~= player and toggles.enemyHighlight then
			local char = p.Character
			if char and char:FindFirstChild("HumanoidRootPart") and not char:FindFirstChild("EnemyHighlight") then
				local h = Instance.new("Highlight")
				h.Name = "EnemyHighlight"
				h.FillColor = Color3.fromRGB(255, 0, 0)
				h.OutlineColor = Color3.fromRGB(255, 255, 255)
				h.FillTransparency = 0.5
				h.OutlineTransparency = 0
				h.Adornee = char
				h.Parent = char
			end
		elseif p.Character and p.Character:FindFirstChild("EnemyHighlight") then
			p.Character.EnemyHighlight:Destroy()
		end
	end
end

-- ESP pets com variações
local function updatePetESP()
	for _, descendant in ipairs(workspace:GetDescendants()) do
		if descendant:IsA("TextLabel") and toggles.petESP then
			local fullText = descendant.Text
			local cleaned = fullText:gsub("[Bb]rainrot", ""):gsub("^%s*(.-)%s*$", "%1")

			local isGod, isSecret = false, false
			local petType = "Normal"
			local petColor = Color3.fromRGB(255, 255, 255)

			if cleaned:lower():find("gold") then
				petType = "Gold"
				petColor = Color3.fromRGB(255, 255, 0)
			elseif cleaned:lower():find("diamond") or cleaned:lower():find("diamante") then
				petType = "Diamante"
				petColor = Color3.fromRGB(100, 255, 255)
			elseif cleaned:lower():find("rainbow") then
				petType = "Rainbow"
				petColor = Color3.fromRGB(255, 100, 255)
			elseif cleaned:lower():find("candy") then
				petType = "Candy"
				petColor = Color3.fromRGB(255, 150, 255)
			elseif cleaned:lower():find("lava") then
				petType = "Lava"
				petColor = Color3.fromRGB(255, 100, 0)
			end

			local baseName = cleaned
				:gsub("^(Gold%s+)", "")
				:gsub("^(Diamond%s+)", "")
				:gsub("^(Diamante%s+)", "")
				:gsub("^(Rainbow%s+)", "")
				:gsub("^(Candy%s+)", "")
				:gsub("^(Lava%s+)", "")
				:gsub("^%s*(.-)%s*$", "%1")

			for _, name in ipairs(godPets) do
				if baseName:lower():find(name:lower(), 1, true) then
					isGod = true
					baseName = name
					break
				end
			end

			if not isGod then
				for _, name in ipairs(secretPets) do
					if baseName:lower():find(name:lower(), 1, true) then
						isSecret = true
						baseName = name
						break
					end
				end
			end

			if isGod or isSecret then
				local adorneeModel = descendant:FindFirstAncestorOfClass("Model")
				if adorneeModel and not adorneeModel:FindFirstChild("PetESP_Active") then
					local highlight = Instance.new("Highlight")
					highlight.Name = "PetESP_Active"
					highlight.Adornee = adorneeModel
					highlight.FillTransparency = 0.2
					highlight.OutlineTransparency = 0
					highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
					highlight.FillColor = isGod and Color3.fromRGB(0, 255, 255) or Color3.fromRGB(170, 0, 255)
					highlight.Parent = adorneeModel
				end

				if adorneeModel and not adorneeModel:FindFirstChild("PetESP_Label") then
					local billboard = Instance.new("BillboardGui")
					billboard.Name = "PetESP_Label"
					billboard.Size = UDim2.new(0, 200, 0, 50)
					billboard.StudsOffset = Vector3.new(0, 3, 0)
					billboard.Adornee = adorneeModel:FindFirstChild("HumanoidRootPart") or adorneeModel.PrimaryPart
					billboard.AlwaysOnTop = true

					local l = Instance.new("TextLabel")
					l.Size = UDim2.new(1, 0, 1, 0)
					l.BackgroundTransparency = 1
					l.TextColor3 = petColor
					l.TextStrokeTransparency = 0.25
					l.Font = Enum.Font.GothamBold
					l.TextSize = 12
					l.TextScaled = false
					l.Text = petType .. " " .. baseName .. (isGod and " [GOD]" or " [SECRET]")
					l.Parent = billboard
					billboard.Parent = adorneeModel
				end
			end
		end

		if not toggles.petESP then
			local model = descendant:FindFirstAncestorOfClass("Model")
			if model then
				local h = model:FindFirstChild("PetESP_Active")
				if h then h:Destroy() end
				local b = model:FindFirstChild("PetESP_Label")
				if b then b:Destroy() end
			end
		end
	end
end

-- Loop principal
RunService.Heartbeat:Connect(function()
	applyGod()
	updateEnemyHighlights()
	updatePetESP()
end)

-- Pulo infinito (MODIFICADO: apenas este bloco)
UserInputService.JumpRequest:Connect(function()
	if toggles.jump then
		local char = player.Character
		if char then
			local hum = char:FindFirstChildOfClass("Humanoid")
			if hum then
				-- Usar JumpPower/Jump em vez de forçar ChangeState
				hum.UseJumpPower = true
				hum.JumpPower = jumpValue
				hum.Jump = true
			end
		end
	end
end)

print("[Ultimate Script] Pronto para uso!")
