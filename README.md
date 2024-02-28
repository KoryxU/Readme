### Sobre mim:
Meu nome é [KoryxU](https://www.roblox.com/users/3632240168/profile), sou desenvolvedor de jogos no Roblox, e já trabalhei em alguns projetos. Aqui quero apresentar alguns dos meus trabalhos com .lua dentro do Roblox Studio.
### Meus projetos:
<details>
<summary>Damage_Indicator</summary>

# About:
Eu criei essa ModuleScript na intenção de ajudar alguns desenvolvedores que não tem muita experiência com .lua no Roblox, que estão querendo criar um jogo de batalha e anime. O código é bem simples de ser usado, é apenas necessário chamar a função ```ApplyIndicator()``` usando o modelo do jogador como argumento, o indicador também pode ser adicionado em qualquer objeto que possua uma Humanoid e um corpo, seja um jogador ou um NPC.

# Server:
```lua
local DamageIndicator = require(game:GetService("ReplicatedStorage").DamageIndicator)

local function onPlayerAdded(player: Player)
	player.CharacterAdded:Connect(function(character)
		-- Adicionar o indicador no jogador quando o boneco dele for criado no jogo
		DamageIndicator.ApplyIndicator(character)
	end)
end

game:GetService("Players").PlayerAdded:Connect(onPlayerAdded)

for _, v in pairs(workspace.Npcs:GetChildren()) do
	-- Adicionar o indicador em vários dummy's ou npc's
	local humanoid = v:FindFirstChild("Humanoid")
	if not humanoid then return end
	
	DamageIndicator.ApplyIndicator(humanoid.Parent)
end
```

# Module:
```lua
local TweenService = game:GetService("TweenService")

local tweenInfo = TweenInfo.new(1, Enum.EasingStyle.Linear, Enum.EasingDirection.Out, 0, false, 0)

local DamageIndicator = {}

function DamageIndicator.ApplyIndicator(Model: Model)
	local humanoid: Humanoid = Model:WaitForChild("Humanoid", 5)
	if not humanoid then error("Model não tem Humanoid.") return end
	
	local currentHealth = humanoid.Health
	local labelDuration = 3
	
	warn("Indicador de dano aplicado no Model: " .. Model.Name .. ".")
	
	humanoid.HealthChanged:Connect(function(health: number)
		if (health < currentHealth and not (humanoid:GetState() == Enum.HumanoidStateType.Dead)) then
			local gui = script.Gui:Clone()
			local text = gui.Text
			
			local result: number = (currentHealth - health)
			
			text.Text = "-" .. tostring(math.floor(result))
			
			task.delay(labelDuration, function(...)
				gui:Destroy()
			end)
			
			gui.Adornee = (humanoid.RootPart or Model.Torso or Model.PrimaryPart)
			gui.ExtentsOffset = Vector3.new(math.random(-3, 3), math.random(1, 2), 0)
			gui.Parent = Model
			
			TweenService:Create(gui, tweenInfo, {ExtentsOffset = gui.ExtentsOffset + Vector3.new(0, gui.ExtentsOffset.Y * 1.5, 0)}):Play()
			TweenService:Create(text, tweenInfo, {TextTransparency = 1}):Play()
		end
		
		currentHealth = health
	end)
end

return DamageIndicator
```

# Source:
https://create.roblox.com/store/asset/16557995812/DamageIndicatorModule
</details>
