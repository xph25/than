local player = game.Players.LocalPlayer

-- HumanoidRootPartを取得する関数
local function getHumanoidRootPart()
    local character = player.Character or player.CharacterAdded:Wait()
    return character:FindFirstChild("HumanoidRootPart")
end

-- Lavaを1秒ごとに削除するループ
local function removeLava()
    while true do
        local map = game.Workspace:FindFirstChild("Map")
        if map then
            local ghostShipInterior = map:FindFirstChild("GhostShipInterior")
            if ghostShipInterior then
                local lavaParts = ghostShipInterior:FindFirstChild("LavaParts")
                if lavaParts then
                    local lava = lavaParts:FindFirstChild("Lava")
                    if lava then
                        lava:Destroy()
                        print("Lava を削除しました。")
                    end
                end
            end
        end
        task.wait(1)
    end
end

task.spawn(removeLava)

-- すでにGUIがある場合は削除
if player.PlayerGui:FindFirstChild("TeleportGui") then
    player.PlayerGui.TeleportGui:Destroy()
end

-- GUI作成
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "TeleportGui"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-- ゆうれいテレポートボタン
local ghostButton = Instance.new("TextButton")
ghostButton.Size = UDim2.new(0, 150, 0, 50)
ghostButton.Position = UDim2.new(0, 50, 0, 100)
ghostButton.BackgroundColor3 = Color3.fromRGB(0, 170, 255)
ghostButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ghostButton.Text = "ゆうれい"
ghostButton.Font = Enum.Font.SourceSansBold
ghostButton.TextSize = 24
ghostButton.Parent = screenGui
ghostButton.Active = true
ghostButton.Draggable = true

ghostButton.MouseButton1Click:Connect(function()
    local hrp = getHumanoidRootPart()
    if hrp then
        hrp.CFrame = CFrame.new(938, 249, 32888)
        print("テレポート：ゆうれい")
    else
        warn("HumanoidRootPartが見つかりません。")
    end
end)

-- まめつぶぐみ テレポートボタン
local beanButton = Instance.new("TextButton")
beanButton.Size = UDim2.new(0, 150, 0, 50)
beanButton.Position = UDim2.new(0, 50, 0, 170)
beanButton.BackgroundColor3 = Color3.fromRGB(255, 50, 0)
beanButton.TextColor3 = Color3.fromRGB(255, 255, 255)
beanButton.Text = "まめつぶぐみ"
beanButton.Font = Enum.Font.SourceSansBold
beanButton.TextSize = 20
beanButton.Parent = screenGui
beanButton.Active = true
beanButton.Draggable = true

beanButton.MouseButton1Click:Connect(function()
    local hrp = getHumanoidRootPart()
    if hrp then
        hrp.CFrame = CFrame.new(2284, 15, 911)
        print("テレポート：まめつぶぐみ")
    else
        warn("HumanoidRootPartが見つかりません。")
    end
end)
