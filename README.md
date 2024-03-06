### Sobre mim:
Meu nome é [KoryxU](https://www.roblox.com/users/3632240168/profile), e aqui vocês podem dar uma olhada em alguns projetos que já fiz no Roblox Studio. Estou há mais ou menos três anos programando e aprendendo Luau, e aceito qualquer sugestão para melhorar meus códigos, tanto em práticas quanto em legibilidade. 😊

### Meus projetos & códigos:
<details>
<summary>Damage Indicator</summary>

## Sobre:
Eu criei essa ModuleScript na intenção de ajudar alguns desenvolvedores que não tem muita experiência com .lua no Roblox, que estão querendo criar um jogo de batalha e anime. O código é bem simples de ser usado, é apenas necessário chamar a função ```ApplyIndicator()``` usando o modelo do jogador como argumento, o indicador também pode ser adicionado em qualquer objeto que possua uma Humanoid e um corpo.

## Script:
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
	DamageIndicator.ApplyIndicator(v)
end
```

## ModuleScript:
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

## Model:
https://create.roblox.com/store/asset/16557995812/DamageIndicatorModule
</details>

<details>
<summary>Conqueror's Haki</summary>

## Sobre:
Este projeto foi desenvolvido por mim para um jogo baseado em One Piece. Embora todos os efeitos sonoros e visuais não tenham sido criados por mim, fui responsável por criar os códigos e a lógica desse Haki. No entanto, não estarei disponibilizando os códigos usados para criar esse Haki, uma vez que foi um trabalho específico para o jogo. Estou compartilhando para que possam conhecer um pouco mais do meu trabalho.

## Vídeo:
https://github.com/KoryxU/Readme/assets/99298307/729ec6f6-785d-43ac-a2b0-14639e612050
</details>

<details>
<summary>Highlighting ProximityPrompt</summary>

## Sobre:
Quando um jogador se aproxima de um objeto com um ProximityPrompt que tem uma tag no CollectionService, o objeto ganha um destaque visual (um “Highlight”). Esse destaque some quando o jogador se afasta, mas quando o jogador aperta o prompt, o objeto fica verde aos poucos.

Esse código foi criado baseado em uma idéia que eu vi em um servidor de desenvolvedores, basta criar um código dentro do ```StarterCharacterScripts```, e colar o código dentro dele.

## LocalScript:
```lua
--// Lembre-se, coloque esse script dentro do StarterCharacterScripts.
--// Esse código foi feito com a idéia de um outro desenvolvedor que eu vi!
--// Crie uma tag para os seus ProximityPrompt em "Editor de Marcadores", e adicione os prompts na tag.
local CollectionService = game:GetService("CollectionService")
local TweenService = game:GetService("TweenService")

local tweenInfo = TweenInfo.new(0.2, Enum.EasingStyle.Linear, Enum.EasingDirection.InOut, 0, false, 0)

for _, v: ProximityPrompt in pairs(CollectionService:GetTagged("ProximityPrompt")) do
	v.PromptShown:Connect(function(inputType: Enum.ProximityPromptInputType)
		local target = v.Parent
		if not target:IsA("BasePart") then return end

		local highlight = (target:FindFirstChild("Highlight") or Instance.new("Highlight"))
		highlight.FillColor = Color3.fromRGB(68, 255, 0)
		highlight.FillTransparency = 1
		highlight.OutlineTransparency = 1
		highlight.Parent = target

		TweenService:Create(highlight, tweenInfo, {OutlineTransparency = 0}):Play()
	end)
	
	v.PromptButtonHoldBegan:Connect(function(playerWhoTriggered: Player)
		local highlight = v.Parent:FindFirstChild("Highlight")
		if not highlight then return end

		local tweenTransparency = TweenService:Create(highlight, TweenInfo.new(v.HoldDuration, Enum.EasingStyle.Linear, Enum.EasingDirection.InOut, 0, false, 0), {FillTransparency = 0.6})
		tweenTransparency:Play()

		tweenTransparency.Completed:Connect(function(playbackState: Enum.PlaybackState)
			TweenService:Create(highlight, tweenInfo, {FillTransparency = 1}):Play()
		end)
	end)
	
	v.PromptButtonHoldEnded:Connect(function(playerWhoTriggered: Player)
		local highlight = v.Parent:FindFirstChild("Highlight")
		if not highlight then return end

		TweenService:Create(highlight, tweenInfo, {FillTransparency = 1}):Play()
	end)

	v.Triggered:Connect(function(playerWhoTriggered: Player)
		local highlight = v.Parent:FindFirstChild("Highlight")
		if not highlight or highlight.FillTransparency == 1 then return end

		TweenService:Create(highlight, tweenInfo, {FillTransparency = 1}):Play()
	end)
	
	v.PromptHidden:Connect(function(...)
		local highlight = v.Parent:FindFirstChild("Highlight")
		if not highlight then return end

		local tweenTransparency = TweenService:Create(highlight, tweenInfo, {OutlineTransparency = 1})
		tweenTransparency:Play()

		tweenTransparency.Completed:Connect(function()
			highlight:Destroy()
		end)
	end)
end
```
</details>

<img src="https://luau-lang.org/assets/images/luau-88.png">
