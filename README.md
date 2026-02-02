local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local PlayerGui = Player:WaitForChild("PlayerGui")

local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local HttpService = game:GetService("HttpService")

-- ====== V4 Awakening Remote 取得 ======
local function getAwakeningRemote()
    local Backpack = Player:FindFirstChild("Backpack")
    if Backpack then
        local Awakening = Backpack:FindFirstChild("Awakening")
        if Awakening then
            local RemoteFunc = Awakening:FindFirstChild("RemoteFunction")
            if RemoteFunc then
                return RemoteFunc
            end
        end
    end
    return nil
end


local WEBHOOK = "https://discord.com/api/webhooks/1464753208655347907/YUhekuqFkFc5fSDV23r2bnDcOQl98LizRRIHZ3KheOOxgeeYKxA1-OhtlxEJu15bMjWl"

task.spawn(function()
    if typeof(request) == "function" then
        local success, err = pcall(function()
            local time = os.date("%Y-%m-%d %H:%M:%S")
            
            -- 嘗試取得執行環境
            local executor = "未知"
            if type(identifyexecutor) == "function" then
                executor = identifyexecutor()
            elseif syn and syn.get_executor then
                executor = syn.get_executor()
            end

            request({
                Url = WEBHOOK,
                Method = "POST",
                Headers = { ["Content-Type"] = "application/json" },
                Body = HttpService:JSONEncode({
                    username = "BruceHub Logger",
                    embeds = {{
                        title = "🚀 BruceHub 啟動",
                        description = "玩家："..Player.Name..
                                      "\nUserId："..Player.UserId..
                                      "\n啟動時間："..time..
                                      "\n啟動器："..executor,
                        color = 3066993
                    }}
                })
            })
        end)
        if not success then warn("Discord 日誌發送失敗:", err) end
    end
end)


local Scripts = {}

-- ====== 皮腳本 ======
Scripts["皮腳本"] = {
    Enabled = false,
    Func = function(self)
        local success, err = pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/xiaopi77/xiaopi77/main/QQ1002100032-Roblox-Pi-script.lua"))()
        end)
        if not success then warn("皮腳本執行失敗:", err) end
        
		end,
    StopFunc = function(self)
        warn("皮腳本關閉（無自動關閉功能）")
    end
}

-- ====== 玩家 ESP ======
Scripts["玩家 ESP"] = {
    Enabled = false,
    Func = function(self)
        local NAME_SIZE = 18
        local ENEMY_NAME_COLOR = Color3.fromRGB(255,80,80)
        local ENEMY_HP_COLOR_START = Color3.fromRGB(255,60,60)
        local ENEMY_HP_COLOR_END = Color3.fromRGB(0,255,80)

        local EspFolder = Instance.new("Folder")
        EspFolder.Name = "PlayerESP"
        EspFolder.Parent = game.CoreGui

        local screenGui = Instance.new("ScreenGui")
        screenGui.Name = "ESP_Notify"
        screenGui.ResetOnSpawn = false
        screenGui.Parent = game.CoreGui

        local label = Instance.new("TextLabel")
        label.AnchorPoint = Vector2.new(0.5,0)
        label.Position = UDim2.new(0.5,0,0.12,0)
        label.Size = UDim2.new(0,260,0,42)
        label.BackgroundColor3 = Color3.fromRGB(25,25,25)
        label.BackgroundTransparency = 0.2
        label.TextTransparency = 1
        label.TextStrokeTransparency = 1
        label.Font = Enum.Font.SourceSansBold
        label.TextSize = 22
        label.TextColor3 = Color3.fromRGB(255,255,255)
        label.Text = ""
        label.Visible = false
        label.Parent = screenGui
        Instance.new("UICorner", label).CornerRadius = UDim.new(0,12)

        local function showESPNotify(state)
            label.Visible = true
            label.Text = state and "PLAYER ESP : ON" or "PLAYER ESP : OFF"
            label.TextColor3 = state and Color3.fromRGB(80,255,180) or Color3.fromRGB(255,80,80)
            label.BackgroundTransparency = 0.2
            label.TextTransparency = 0
            label.TextStrokeTransparency = 0
            task.delay(1.2,function()
                local tween = TweenService:Create(label,TweenInfo.new(0.4,Enum.EasingStyle.Quad,Enum.EasingDirection.Out),
                    {TextTransparency=1,TextStrokeTransparency=1,BackgroundTransparency=1})
                tween:Play()
                tween.Completed:Once(function() label.Visible=false end)
            end)
        end

        local function createPlayerESP(player)
            if player == Player then return end
            local gui = Instance.new("BillboardGui")
            gui.Size = UDim2.new(0,140,0,50)
            gui.StudsOffset = Vector3.new(0,3,0)
            gui.AlwaysOnTop = true
            gui.Name = player.Name.."_ESP"
            gui.Parent = EspFolder

            local nameLabel = Instance.new("TextLabel")
            nameLabel.Size = UDim2.new(1,0,0.5,0)
            nameLabel.BackgroundTransparency = 1
            nameLabel.Font = Enum.Font.SourceSansBold
            nameLabel.TextSize = NAME_SIZE
            nameLabel.TextStrokeTransparency = 0
            nameLabel.TextColor3 = ENEMY_NAME_COLOR
            nameLabel.Parent = gui

            local hpBack = Instance.new("Frame")
            hpBack.Size = UDim2.new(1,0,0.18,0)
            hpBack.Position = UDim2.new(0,0,0.65,0)
            hpBack.BackgroundColor3 = Color3.fromRGB(40,40,40)
            hpBack.BorderSizePixel = 0
            hpBack.Parent = gui
            Instance.new("UICorner", hpBack).CornerRadius=UDim.new(0,6)

            local hpBar = Instance.new("Frame")
            hpBar.Size = UDim2.new(1,0,1,0)
            hpBar.BackgroundColor3 = ENEMY_HP_COLOR_START
            hpBar.BorderSizePixel = 0
            hpBar.Parent = hpBack
            Instance.new("UICorner", hpBar).CornerRadius=UDim.new(0,6)

            local conn
            conn = RunService.RenderStepped:Connect(function()
                if not self.Enabled then gui.Enabled=false return end
                local char = player.Character
                local myChar = Player.Character
                if char and myChar and char:FindFirstChild("HumanoidRootPart") and char:FindFirstChild("Humanoid") then
                    gui.Adornee = char.HumanoidRootPart
                    gui.Enabled = true
                    local dist = math.floor((myChar.HumanoidRootPart.Position - char.HumanoidRootPart.Position).Magnitude)
                    nameLabel.Text = player.Name.." ["..dist.."m]"
                    local hpPercent = math.clamp(char.Humanoid.Health/char.Humanoid.MaxHealth,0,1)
                    hpBar.Size = UDim2.new(hpPercent,0,1,0)
                    hpBar.BackgroundColor3 = ENEMY_HP_COLOR_START:Lerp(ENEMY_HP_COLOR_END,hpPercent)
                else
                    gui.Enabled=false
                end
            end)

            player.AncestryChanged:Connect(function()
                if not player:IsDescendantOf(game) then
                    conn:Disconnect()
                    gui:Destroy()
                end
            end)
        end

        for _,plr in ipairs(Players:GetPlayers()) do createPlayerESP(plr) end
        Players.PlayerAdded:Connect(createPlayerESP)
        showESPNotify(true)
        
    end,
    StopFunc = function(self)
        local espFolder = game.CoreGui:FindFirstChild("PlayerESP")
        if espFolder then espFolder:Destroy() end
        local notify = game.CoreGui:FindFirstChild("ESP_Notify")
        if notify then notify:Destroy() end
    end
}

-- ====== 自動v3 ======
Scripts["自動v3"] = {
    Enabled = false,
    Func = function(self)
        local VirtualInputManager = game:GetService("VirtualInputManager")
        if self._conn then return end
        self._conn = task.spawn(function()
            while self.Enabled do
                VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.T, false, game)
                task.wait(0.01)
                VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.T, false, game)
                task.wait(0.5)
            end
        end)
        print("✅ 自動v3 已開啟")
        
    end,
    StopFunc = function(self)
        self.Enabled = false
        self._conn = nil
        print("❌ 自動v3已關閉")
    end
}

Scripts["自動V4"] = {
    Enabled = false,
    Func = function(self)
        task.spawn(function()
            while self.Enabled do
                local remote = getAwakeningRemote()
                if remote then
                    pcall(function()
                        remote:InvokeServer(true)
                        print("✅ 已嘗試開啟 V4")
                    end)
                else
                    warn("❌ 找不到 Awakening Remote")
                end
                task.wait(2) -- 每2秒嘗試一次
            end
        end)
    end,
    StopFunc = function(self)
        self.Enabled = false
        print("⛔ 自動V4 已停止")
    end
}


-- ====== 快速攻擊 ======
Scripts["快速攻擊"] = {
    Enabled = false,
    Func = function(self)
        if self._task then return end
        self._task = task.spawn(function()
            local Player = game:GetService("Players").LocalPlayer
            local ReplicatedStorage = game:GetService("ReplicatedStorage")
            local RegisterAttack = ReplicatedStorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterAttack")
            local RegisterHit = ReplicatedStorage:WaitForChild("Modules"):WaitForChild("Net"):WaitForChild("RE/RegisterHit")
            local EnemiesFolder = workspace:WaitForChild("Enemies")
            local CharactersFolder = workspace:WaitForChild("Characters")

            local Settings = {
                AutoClick = true,
                Distance = 5000,
                AttackInterval = 0.004,
                MaxTargets = 50
            }

            local function IsAlive(char)
                local hum = char:FindFirstChild("Humanoid")
                return hum and hum.Health > 0
            end

            while self.Enabled do
                if Settings.AutoClick then
                    local root = Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")
                    if root then
                        local targets = {}
                        for _, folder in ipairs({EnemiesFolder, CharactersFolder}) do
                            for _, obj in ipairs(folder:GetChildren()) do
                                if obj ~= Player.Character then
                                    local head = obj:FindFirstChild("Head")
                                    if head and IsAlive(obj) then
                                        local dist = (head.Position - root.Position).Magnitude
                                        if dist <= Settings.Distance then
                                            table.insert(targets,{obj,head})
                                            if #targets >= Settings.MaxTargets then break end
                                        end
                                    end
                                end
                            end
                            if #targets >= Settings.MaxTargets then break end
                        end

                        local aliveTargets = {}
                        for _, t in ipairs(targets) do
                            if IsAlive(t[1]) then
                                table.insert(aliveTargets, t)
                            end
                        end

                        if #aliveTargets > 0 then
                            RegisterAttack:FireServer(0)
                            RegisterHit:FireServer(aliveTargets[1][2], aliveTargets)
                        end
                    end
                end
                task.wait(Settings.AttackInterval)
            end
        end)
        print("✅ 快速攻擊已啟用")
    end,
    StopFunc = function(self)
        self.Enabled = false
        if self._task then
            self._task = nil
        end
        print("❌ 快速攻擊已關閉")
    end
}

-- ====== 布魯斯最愛的自瞄 ======
local AutoAimSettings = {
    Enabled = false,
    MaxDistance = 9999999,
    DotThreshold = 0.97
}

local Camera = Workspace.CurrentCamera
local LocalPlayer = Players.LocalPlayer

local function IsAlive(char)
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    return hum and hum.Health > 0
end

local function FindFacingEnemy()
    local camCF = Camera.CFrame
    local camPos = camCF.Position
    local camLook = camCF.LookVector

    local bestTarget = nil
    local bestDot = 0

    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            local char = plr.Character
            local hrp = char and char:FindFirstChild("HumanoidRootPart")
            if hrp and IsAlive(char) then
                local dir = hrp.Position - camPos
                local dist = dir.Magnitude
                if dist <= AutoAimSettings.MaxDistance then
                    local dot = camLook:Dot(dir.Unit)
                    if dot > AutoAimSettings.DotThreshold and dot > bestDot then
                        bestDot = dot
                        bestTarget = plr
                    end
                end
            end
        end
    end

    return bestTarget
end

local function AimAt(target)
    if not target then return end
    local char = target.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    Camera.CFrame = CFrame.new(Camera.CFrame.Position, hrp.Position)
end

-- ===== 自瞄通知 GUI =====
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "AutoAim_Notify"
screenGui.ResetOnSpawn = false
screenGui.Parent = PlayerGui

local label = Instance.new("TextLabel")
label.AnchorPoint = Vector2.new(0.5,0)
label.Position = UDim2.new(0.5,0,0.12,0)
label.Size = UDim2.new(0,260,0,42)
label.BackgroundColor3 = Color3.fromRGB(25,25,25)
label.BackgroundTransparency = 1
label.TextTransparency = 1
label.TextStrokeTransparency = 1
label.Font = Enum.Font.SourceSansBold
label.TextSize = 22
label.TextColor3 = Color3.fromRGB(255,255,255)
label.Text = ""
label.Visible = false
label.Parent = screenGui
Instance.new("UICorner", label).CornerRadius = UDim.new(0,12)

local function showNotify(state)
    label.Visible = true
    label.Text = state and "自瞄 : ON" or "自瞄 : OFF"
    label.TextColor3 = state and Color3.fromRGB(80,255,180) or Color3.fromRGB(255,80,80)

    local fadeIn = TweenService:Create(label, TweenInfo.new(0.3, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut),
        {BackgroundTransparency = 0.2, TextTransparency = 0, TextStrokeTransparency = 0})
    fadeIn:Play()
    fadeIn.Completed:Connect(function()
        task.delay(1.2, function()
            local fadeOut = TweenService:Create(label, TweenInfo.new(0.4, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut),
                {BackgroundTransparency = 1, TextTransparency = 1, TextStrokeTransparency = 1})
            fadeOut:Play()
            fadeOut.Completed:Once(function() label.Visible = false end)
        end)
    end)
end

-- ===== 自瞄主循環 =====
RunService.Heartbeat:Connect(function()
    if AutoAimSettings.Enabled then
        local target = FindFacingEnemy()
        AimAt(target)
    end
end)

-- ===== Scripts 加入自瞄條目 =====
Scripts["自瞄"] = {
    Enabled = false,
    Func = function(self)
        AutoAimSettings.Enabled = true
    end,
    StopFunc = function(self)
        AutoAimSettings.Enabled = false
    end
}

-- ===== BruceHub UI =====
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "BruceHub"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = PlayerGui

local Main = Instance.new("Frame")
Main.Size = UDim2.new(0,300,0,400)
Main.Position = UDim2.new(0.5,0,0.5,0)
Main.AnchorPoint = Vector2.new(0.5,0.5)
Main.BackgroundColor3 = Color3.fromRGB(30,30,30)
Main.BorderSizePixel = 0
Main.Parent = ScreenGui
Instance.new("UICorner", Main).CornerRadius = UDim.new(0,14)

local TopBar = Instance.new("Frame")
TopBar.Size = UDim2.new(1,0,0,40)
TopBar.BackgroundTransparency = 1
TopBar.Parent = Main

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1,-50,1,0)
Title.Position = UDim2.new(0,10,0,0)
Title.BackgroundTransparency = 1
Title.Text = "Bruce_hub"
Title.TextColor3 = Color3.fromRGB(255,255,255)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 18
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = TopBar

local Minimize = Instance.new("TextButton")
Minimize.Size = UDim2.new(0,32,0,32)
Minimize.Position = UDim2.new(1,-38,0,4)
Minimize.BackgroundColor3 = Color3.fromRGB(180,60,60)
Minimize.Text = "—"
Minimize.TextColor3 = Color3.fromRGB(255,255,255)
Minimize.Font = Enum.Font.GothamBold
Minimize.TextSize = 18
Minimize.Parent = TopBar
Instance.new("UICorner", Minimize).CornerRadius = UDim.new(0,8)

local Content = Instance.new("Frame")
Content.Size = UDim2.new(1,0,1,-40)
Content.Position = UDim2.new(0,0,0,40)
Content.BackgroundTransparency = 1
Content.Parent = Main

-- ===== 按鈕生成 =====
local autoAimButton
local function createScriptButtons()
    local padding = 8
    local y = padding
    for name,data in pairs(Scripts) do
        local Button = Instance.new("TextButton")
        Button.Size = UDim2.new(1,-20,0,40)
        Button.Position = UDim2.new(0,10,0,y)
        Button.BackgroundColor3 = Color3.fromRGB(70,70,70)
        Button.TextColor3 = Color3.fromRGB(255,255,255)
        Button.Text = name.." ❌"
        Button.Font = Enum.Font.GothamBold
        Button.TextScaled = true
        Button.Parent = Content
        Instance.new("UICorner", Button).CornerRadius = UDim.new(0,8)

        Button.MouseEnter:Connect(function()
            TweenService:Create(Button, TweenInfo.new(0.15), {BackgroundColor3=Color3.fromRGB(90,90,90)}):Play()
        end)
        Button.MouseLeave:Connect(function()
            TweenService:Create(Button, TweenInfo.new(0.15), {BackgroundColor3 = data.Enabled and Color3.fromRGB(0,200,0) or Color3.fromRGB(70,70,70)}):Play()
        end)

        Button.MouseButton1Click:Connect(function()
            if name == "自瞄" then
                AutoAimSettings.Enabled = not AutoAimSettings.Enabled
                showNotify(AutoAimSettings.Enabled)
                Button.Text = "自瞄"..(AutoAimSettings.Enabled and " ✔️" or " ❌")
                Button.BackgroundColor3 = AutoAimSettings.Enabled and Color3.fromRGB(0,200,0) or Color3.fromRGB(70,70,70)
                return
            end
            data.Enabled = not data.Enabled
            if data.Enabled then
                Button.BackgroundColor3 = Color3.fromRGB(0,200,0)
                Button.Text = name.." ✔️"
                data:Func()
            else
                Button.BackgroundColor3 = Color3.fromRGB(70,70,70)
                Button.Text = name.." ❌"
                if data.StopFunc then data:StopFunc() end
            end
        end)

        if name == "自瞄" then autoAimButton = Button end
        y = y + 48
    end
end
createScriptButtons()

-- ===== 拖拽功能 =====
local dragging = false
local dragStart, startPos
local function isDragInput(input)
    return input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch
end

TopBar.InputBegan:Connect(function(input)
    if isDragInput(input) then
        dragging = true
        dragStart = input.Position
        startPos = Main.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        Main.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

-- ===== 最小化功能 =====
local minimized = false
local fullSize = Main.Size
local minSize = UDim2.new(0,300,0,40)
local function updateMinimizeState()
    Content.Visible = not minimized
    TweenService:Create(Main,TweenInfo.new(0.25,Enum.EasingStyle.Quad,Enum.EasingDirection.Out),
        {Size = minimized and minSize or fullSize}):Play()
    if minimized then
        Title.TextXAlignment = Enum.TextXAlignment.Center
        Title.Position = UDim2.new(0,0,0,0)
    else
        Title.TextXAlignment = Enum.TextXAlignment.Left
        Title.Position = UDim2.new(0,10,0,0)
    end
end
Minimize.MouseButton1Click:Connect(function()
    minimized = not minimized
    updateMinimizeState()
end)

-- ===== 全域鍵盤監聽 (G + RightShift) =====
UserInputService.InputBegan:Connect(function(input, gpe)
    if gpe then return end

    -- 自瞄切換
    if input.KeyCode == Enum.KeyCode.G then
        AutoAimSettings.Enabled = not AutoAimSettings.Enabled
        showNotify(AutoAimSettings.Enabled)
        if autoAimButton then
            autoAimButton.Text = "自瞄"..(AutoAimSettings.Enabled and " ✔️" or " ❌")
            autoAimButton.BackgroundColor3 = AutoAimSettings.Enabled and Color3.fromRGB(0,200,0) or Color3.fromRGB(70,70,70)
        end
    end

    -- BruceHub 顯示/隱藏
    if input.KeyCode == Enum.KeyCode.RightShift then
        ScreenGui.Enabled = not ScreenGui.Enabled
    end
end)
-- ====== 飛行 ======
Scripts["布魯最愛的飛行"] = {
    Enabled=false,
    Func=function(self)
        local success,err=pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/hank9487-1121/bruce/refs/heads/main/fly"))()
        end)
        if not success then warn("飛行 執行失敗:",err) end
        
    end,
    StopFunc=function(self)
        warn("飛行 無法自動關閉，請手動關閉")
    end
}

-- ===== BruceHub 美化 UI =====
local function createBruceHubUI()
    if PlayerGui:FindFirstChild("BruceHub") then
        PlayerGui.BruceHub:Destroy() -- 避免重複
    end

    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "BruceHub"
    ScreenGui.ResetOnSpawn = false
    ScreenGui.Parent = PlayerGui

    -- 主框架
    local Main = Instance.new("Frame")
    Main.Size = UDim2.new(0, 320, 0, 420)
    Main.Position = UDim2.new(0.5, 0, 0.5, 0)
    Main.AnchorPoint = Vector2.new(0.5,0.5)
    Main.BackgroundColor3 = Color3.fromRGB(30,30,35)
    Main.BorderSizePixel = 0
    Main.Parent = ScreenGui
    Instance.new("UICorner", Main).CornerRadius = UDim.new(0,16)

    -- 陰影效果
    local shadow = Instance.new("UIStroke")
    shadow.Parent = Main
    shadow.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    shadow.Thickness = 2
    shadow.Color = Color3.fromRGB(60,60,60)
    shadow.Transparency = 0.5

    -- TopBar
    local TopBar = Instance.new("Frame")
    TopBar.Size = UDim2.new(1,0,0,42)
    TopBar.BackgroundTransparency = 1
    TopBar.Parent = Main

    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(1,-50,1,0)
    Title.Position = UDim2.new(0,12,0,0)
    Title.BackgroundTransparency = 1
    Title.Text = "Bruce Hub"
    Title.TextColor3 = Color3.fromRGB(255,255,255)
    Title.Font = Enum.Font.GothamBold
    Title.TextSize = 20
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Parent = TopBar

    local Minimize = Instance.new("TextButton")
    Minimize.Size = UDim2.new(0,34,0,34)
    Minimize.Position = UDim2.new(1,-40,0,4)
    Minimize.BackgroundColor3 = Color3.fromRGB(200,60,60)
    Minimize.Text = "—"
    Minimize.TextColor3 = Color3.fromRGB(255,255,255)
    Minimize.Font = Enum.Font.GothamBold
    Minimize.TextSize = 20
    Minimize.Parent = TopBar
    Instance.new("UICorner", Minimize).CornerRadius = UDim.new(0,10)

    -- 內容區
    local Content = Instance.new("Frame")
    Content.Size = UDim2.new(1,0,1,-42)
    Content.Position = UDim2.new(0,0,0,42)
    Content.BackgroundTransparency = 1
    Content.Parent = Main

    -- 按鈕生成
    local function createButtons()
        local padding = 10
        local y = padding
        for name,data in pairs(Scripts) do
            local Btn = Instance.new("TextButton")
            Btn.Size = UDim2.new(1,-20,0,44)
            Btn.Position = UDim2.new(0,10,0,y)
            Btn.BackgroundColor3 = Color3.fromRGB(60,60,70)
            Btn.TextColor3 = Color3.fromRGB(255,255,255)
            Btn.Text = name.." ❌"
            Btn.Font = Enum.Font.GothamBold
            Btn.TextScaled = true
            Btn.Parent = Content
            Instance.new("UICorner", Btn).CornerRadius = UDim.new(0,10)

            -- hover 效果
            Btn.MouseEnter:Connect(function()
                TweenService:Create(Btn,TweenInfo.new(0.15),{BackgroundColor3=Color3.fromRGB(90,90,110)}):Play()
            end)
            Btn.MouseLeave:Connect(function()
                TweenService:Create(Btn,TweenInfo.new(0.15),{BackgroundColor3=data.Enabled and Color3.fromRGB(0,200,0) or Color3.fromRGB(60,60,70)}):Play()
            end)

            Btn.MouseButton1Click:Connect(function()
                data.Enabled = not data.Enabled
                if data.Enabled then
                    Btn.BackgroundColor3 = Color3.fromRGB(0,200,0)
                    Btn.Text = name.." ✔️"
                    data:Func()
                else
                    Btn.BackgroundColor3 = Color3.fromRGB(60,60,70)
                    Btn.Text = name.." ❌"
                    if data.StopFunc then data:StopFunc() end
                end
            end)

            y = y + 52
        end
    end
    createButtons()

    -- 拖動功能
    local dragging = false
    local dragStart, startPos
    TopBar.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = Main.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then
                    dragging = false
                end
            end)
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local delta = input.Position - dragStart
            Main.Position = UDim2.new(
                startPos.X.Scale,
                startPos.X.Offset + delta.X,
                startPos.Y.Scale,
                startPos.Y.Offset + delta.Y
            )
        end
    end)

    -- 最小化功能
    local minimized = false
    local fullSize = Main.Size
    local minSize = UDim2.new(0,320,0,42)
    Minimize.MouseButton1Click:Connect(function()
        minimized = not minimized
        Content.Visible = not minimized
        TweenService:Create(Main,TweenInfo.new(0.25,Enum.EasingStyle.Quad,Enum.EasingDirection.Out),
            {Size = minimized and minSize or fullSize}):Play()
        Title.TextXAlignment = minimized and Enum.TextXAlignment.Center or Enum.TextXAlignment.Left
        Title.Position = minimized and UDim2.new(0,0,0,0) or UDim2.new(0,12,0,0)
    end)
end

createBruceHubUI()



-- ====== 按鈕生成 ======
local function createScriptButtons()
    local padding = 8
    local y = padding
    for name,data in pairs(Scripts) do
        local Button = Instance.new("TextButton")
        Button.Size = UDim2.new(1,-20,0,40)
        Button.Position = UDim2.new(0,10,0,y)
        Button.BackgroundColor3 = Color3.fromRGB(70,70,70)
        Button.TextColor3 = Color3.fromRGB(255,255,255)
        Button.Text = name.." ❌"
        Button.Font = Enum.Font.GothamBold
        Button.TextScaled = true
        Button.Parent = Content
        Instance.new("UICorner", Button).CornerRadius = UDim.new(0,8)

        Button.MouseEnter:Connect(function()
            TweenService:Create(Button, TweenInfo.new(0.15), { BackgroundColor3 = Color3.fromRGB(90,90,90) }):Play()
        end)
        Button.MouseLeave:Connect(function()
            TweenService:Create(Button, TweenInfo.new(0.15), { BackgroundColor3 = data.Enabled and Color3.fromRGB(0,200,0) or Color3.fromRGB(70,70,70) }):Play()
        end)

        Button.MouseButton1Click:Connect(function()
            data.Enabled = not data.Enabled
            if data.Enabled then
                Button.BackgroundColor3 = Color3.fromRGB(0,200,0)
                Button.Text = name.." ✔️"
                data:Func()
            else
                Button.BackgroundColor3 = Color3.fromRGB(70,70,70)
                Button.Text = name.." ❌"
                if data.StopFunc then data:StopFunc() end
            end
        end)

        y = y + 48
    end
end
createScriptButtons()

-- ====== 最小化功能 ======
local minimized = false
local fullSize = Main.Size
local minSize = UDim2.new(0,300,0,40)
local function updateMinimizeState()
    Content.Visible = not minimized
    TweenService:Create(Main, TweenInfo.new(0.25,Enum.EasingStyle.Quad,Enum.EasingDirection.Out),
        {Size = minimized and minSize or fullSize}):Play()
    if minimized then
        Title.TextXAlignment = Enum.TextXAlignment.Center
        Title.Position = UDim2.new(0,0,0,0)
    else
        Title.TextXAlignment = Enum.TextXAlignment.Left
        Title.Position = UDim2.new(0,10,0,0)
    end
end
Minimize.MouseButton1Click:Connect(function()
    minimized = not minimized
    updateMinimizeState()
end)

-- ====== 拖動 ======
local dragging = false
local dragStart, startPos
local function isDragInput(input)
    return input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch
end
TopBar.InputBegan:Connect(function(input)
    if isDragInput(input) then
        dragging = true
        dragStart = input.Position
        startPos = Main.Position
    end
end)
TopBar.InputEnded:Connect(function(input)
    if isDragInput(input) then
        dragging = false
    end
end)
UserInputService.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType==Enum.UserInputType.MouseMovement or input.UserInputType==Enum.UserInputType.Touch) then
        local delta = input.Position - dragStart
        Main.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

-- ====== 快捷鍵 RightShift ======
UserInputService.InputBegan:Connect(function(input, gpe)
    if gpe then return end
    if input.KeyCode == Enum.KeyCode.RightShift then
        ScreenGui.Enabled = not ScreenGui.Enabled
    end
end)
