local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local admins = {
	["SeuNomeAqui"] = true,
}

local trollEvent = Instance.new("RemoteEvent")
trollEvent.Name = "TrollEvent"
trollEvent.Parent = ReplicatedStorage

Players.PlayerAdded:Connect(function(player)
	if not admins[player.Name] then return end

	player.CharacterAdded:Wait()

	local guiScript = [[
		local Players = game:GetService("Players")
		local ReplicatedStorage = game:GetService("ReplicatedStorage")
		local player = Players.LocalPlayer
		local trollEvent = ReplicatedStorage:WaitForChild("TrollEvent")

		local gui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))
		gui.Name = "AdminTrollGUI"

		local nameBox = Instance.new("TextBox")
		nameBox.Size = UDim2.new(0, 160, 0, 40)
		nameBox.Position = UDim2.new(0, 20, 0, 50)
		nameBox.PlaceholderText = "Nome do player"
		nameBox.Text = ""
		nameBox.Parent = gui

		local function makeButton(text, yPos, action)
			local btn = Instance.new("TextButton")
			btn.Size = UDim2.new(0, 160, 0, 35)
			btn.Position = UDim2.new(0, 20, 0, yPos)
			btn.Text = text
			btn.Font = Enum.Font.SourceSansBold
			btn.TextSize = 16
			btn.BackgroundColor3 = Color3.fromRGB(0, 120, 255)
			btn.TextColor3 = Color3.new(1,1,1)
			btn.Parent = gui

			btn.MouseButton1Click:Connect(function()
				local target = nameBox.Text
				if target ~= "" then
					trollEvent:FireServer(target, action)
				end
			end)
		end

		makeButton("Teleportar", 100, "teleport")
		makeButton("Matar", 145, "kill")
		makeButton("Kickar", 190, "kick")
		makeButton("Grudar no teto", 235, "grudar")
		makeButton("Ficar invisível", 280, "invisivel")
	]]

	local localScript = Instance.new("LocalScript")
	localScript.Source = guiScript
	localScript.Name = "AdminTrollGUI_Script"
	localScript.Parent = player:WaitForChild("PlayerGui")
end)

trollEvent.OnServerEvent:Connect(function(sender, targetName, action)
	if not admins[sender.Name] then return end

	local target = Players:FindFirstChild(targetName)
	if not target or not target.Character then return end

	local humanoid = target.Character:FindFirstChild("Humanoid")
	local root = target.Character:FindFirstChild("HumanoidRootPart")

	if action == "kill" and humanoid then
		humanoid.Health = 0
	elseif action == "teleport" and root then
		local pos = Vector3.new(math.random(-100, 100), 50, math.random(-100, 100))
		root.CFrame = CFrame.new(pos)
	elseif action == "kick" then
		target:Kick("Você foi trollado por um moderador 😈")
	elseif action == "grudar" and root then
		root.Anchored = true
		root.CFrame = root.CFrame + Vector3.new(0, 60, 0)
	elseif action == "invisivel" then
		for _, part in ipairs(target.Character:GetDescendants()) do
			if part:IsA("BasePart") or part:IsA("Decal") then
				part.Transparency = 1
			end
		end
	end
end)
