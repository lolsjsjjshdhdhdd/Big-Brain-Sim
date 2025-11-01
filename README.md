local uiname = "Big Brain Sim v2"

local library = loadstring(game:HttpGet("https://raw.githubusercontent.com/liebertsx/Tora-Library/main/src/librarynew", true))()

local tab = library:CreateWindow(uiname) local folder = tab:AddFolder("Main") local Sliders = tab:AddFolder("Slider Stuff") local endt = tab:AddFolder("Close UI")

-- Vars local autoread = false local spamSpeed = 0.1 local coinFarmSpeed = 0.01 local coinfarmActive = false local autosell = false local autobuy = false local autostorage = false

-- 📝 Update noti 📝
game:GetService("StarterGui"):SetCore("SendNotification",{ Title = "UPDATE"; Text = "v2: Add Slider Tab for Smaller Ui For Mobile"; Button1 ="Got It!"; })

-- 🧠 Auto Read (fixed to detect all valid book names)
folder:AddToggle({ text = "Auto Read", flag = "autoread", callback = function(v) autoread = v if autoread then task.spawn(function() local ReplicatedStorage = game:GetService("ReplicatedStorage") local Players = game:GetService("Players") local player = Players.LocalPlayer

			local function findBookClicked()
				local remotes = {"Remotes", "RemoteEvents"}
				for _, rName in ipairs(remotes) do
					local container = ReplicatedStorage:FindFirstChild(rName)
					if container and container:FindFirstChild("BookClicked") then
						return container.BookClicked
					end
				end
				return ReplicatedStorage:FindFirstChild("BookClicked")
			end

			local function findBooksFolder()
				local possibleNames = {
					player.Name .. "_scripts",
					player.Name .. "_Scripts",
					player.Name,
					"Bora_scripts",
					"Books",
					"Scripts",
				}
				for _, name in ipairs(possibleNames) do
					local f = workspace:FindFirstChild(name)
					if f then return f end
				end
				for _, child in ipairs(workspace:GetChildren()) do
					if child:IsA("Folder") then
						for _, g in ipairs(child:GetChildren()) do
							if string.find(g.Name:lower(), "book") or string.find(g.Name:lower(), "dictionary") or string.find(g.Name:lower(), "oof") then
								return child
							end
						end
					end
				end
				return nil
			end

			local function getPlayerBook(f)
				if not f then return nil end
				for _, obj in ipairs(f:GetChildren()) do
					if obj:IsA("Model") or obj:IsA("Tool") or obj:IsA("Part") then
						local owner = obj:FindFirstChild("Owner")
						if owner and (owner.Value == player or tostring(owner.Value) == player.Name) then
							return obj
						end
						if string.find(obj.Name:lower(), "book") or string.find(obj.Name:lower(), "dictionary") or string.find(obj.Name:lower(), "oof") then
							return obj
						end
					end
				end
				return nil
			end

			local BookClicked = findBookClicked()
			while autoread do
				task.wait(spamSpeed)
				local folderBooks = findBooksFolder()
				if not folderBooks then
					task.wait(1)
					continue
				end
				local playerBook = getPlayerBook(folderBooks)
				if playerBook and BookClicked then
					pcall(function()
						BookClicked:FireServer(playerBook.Name)
					end)
				end
			end
		end)
	end
end
})

-- 💰 Auto Coin Farm
folder:AddToggle({ text = "Auto Coin Farm", flag = "coinauto", callback = function(v) coinfarmActive = v if coinfarmActive then task.spawn(function() local plr = game:GetService("Players").LocalPlayer local hrp = plr.Character and plr.Character:FindFirstChildWhichIsA("BasePart") while coinfarmActive and hrp do task.wait(coinFarmSpeed) for _, v in pairs(workspace.Coins:GetDescendants()) do if v:IsA("TouchTransmitter") and v.Parent:IsA("Part") then local part = v.Parent firetouchinterest(part, hrp, 0) firetouchinterest(part, hrp, 1) end end end end) end end })

-- 📗 Auto Buy Next Book
folder:AddToggle({ text = "Auto Buy Next Book", flag = "autobuy", callback = function(v) autobuy = v if autobuy then task.spawn(function() local ReplicatedStorage = game:GetService("ReplicatedStorage") local Players = game:GetService("Players") local player = Players.LocalPlayer local BuyBook = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("BuyBook")

			local bookOrder = {
                "Blue Book",
                "Green Book",
                "Yellow Book",
                "Purple Book",
                "Orange Book",
                "Lime Book",
                "Violet Book",
                "Magenta Book",
                "White Book",
                "Black Book",
				"Bronze Book",
				"Silver Book",
				"Gold Book",
				"Platinum Book",
				"Titanium Book",
				"Glass Book",
				"Rainbow Book",
				"Shadow Book",
				"Glowing Book",
				"The Dictionary",
				"OOF Book",
				"Fire Book",
				"Cosmic Book"
			}

			local function getCurrentBook()
				local backpack = player:WaitForChild("Backpack")
				for _, tool in pairs(backpack:GetChildren()) do
					if string.find(tool.Name:lower(), "book") or string.find(tool.Name:lower(), "dictionary") or string.find(tool.Name:lower(), "oof") then
						return tool.Name
					end
				end
				local char = player.Character
				if char then
					for _, tool in pairs(char:GetChildren()) do
						if string.find(tool.Name:lower(), "book") or string.find(tool.Name:lower(), "dictionary") or string.find(tool.Name:lower(), "oof") then
							return tool.Name
						end
					end
				end
				return nil
			end

			local function buyNextBook()
				local current = getCurrentBook()
				if not current then return end
				local currentIndex
				for i, name in ipairs(bookOrder) do
					if string.lower(name) == string.lower(current) then
						currentIndex = i
						break
					end
				end
				if not currentIndex then return end
				local nextBook = bookOrder[currentIndex + 1]
				if nextBook then
					pcall(function()
						BuyBook:FireServer(nextBook)
					end)
				end
			end

			while autobuy do
				pcall(buyNextBook)
				task.wait(5)
			end
		end)
	end
end
})

folder:AddLabel({ text = "You will have to buy", type = "label" })

folder:AddLabel({ text = "The blue book", type = "label" })

-- 📦 Auto Upgrade Storage
folder:AddToggle({ text = "Auto Upgrade Storage", flag = "autostorage", callback = function(v) autostorage = v if autostorage then task.spawn(function() local ReplicatedStorage = game:GetService("ReplicatedStorage") local UpgradeCapacity = ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("UpgradeCapacity") while autostorage do pcall(function() UpgradeCapacity:FireServer() end) task.wait(3) end end) end end })

-- ⚙️ Speed Sliders
Sliders:AddSlider({ text = "Auto Read Speed", min = 0.001, max = 10, dual = false, type = "slider", callback = function(v) spamSpeed = v end })

Sliders:AddSlider({ text = "Coin Farm Speed", min = 0.001, max = 10, dual = false, type = "slider", callback = function(v) coinFarmSpeed = v end })

-- 🌍 Unlock All Worlds
folder:AddButton({ text = "Unlock All Worlds", callback = function() local player = game:GetService("Players").LocalPlayer local char = player.Character or player.CharacterAdded:Wait() local hrp = char:WaitForChild("HumanoidRootPart")

	local originalCF = hrp.CFrame
	local highCF = CFrame.new(originalCF.X, 100000, originalCF.Z)
	hrp.CFrame = highCF
	task.wait(0.5)
	hrp.CFrame = originalCF
end
})

-- 💸 Auto Sell
folder:AddToggle({ text = "Auto Sell", flag = "autosell", callback = function(v) autosell = v if autosell then task.spawn(function() while autosell do task.wait(0.5) local sellpart = workspace:FindFirstChild("SellPart") if sellpart then local plr = game:GetService("Players").LocalPlayer local hrp = plr.Character and plr.Character:FindFirstChildWhichIsA("BasePart") if hrp then firetouchinterest(sellpart, hrp, 0) firetouchinterest(sellpart, hrp, 1) end end end end) end end })

-- ❌ Close UI
endt:AddButton({ text = "Close UI", callback = function() library:Close() end })

tab:AddLabel({ text = "By Bora_Scripts", type = "label" })

library:Init()

loadstring(Game:HttpGet("https://raw.githubusercontent.com/lolsjsjjshdhdhdd/My-ui-test/refs/heads/main/Test"))()
