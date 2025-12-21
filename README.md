local TextChatService = game:GetService("TextChatService")
local UserInputService = game:GetService("UserInputService")
local player = game.Players.LocalPlayer

local comandos = { "/mat peto🍫", "/render peto🍫", "/fura pneu peto🍫" }
local iniciais = { ["/mat🍫"] = "M", ["/render🍫"] = "R", ["/fura pneu🍫"] = "F" }
local delays = { ["/mat🍫"] = 0.2, ["/render🍫"] = 0.2, ["/fura pneu🍫"] = 0.2 }
local reps = { ["/mat🍫"] = 1, ["/render🍫"] = 1, ["/fura pneu🍫"] = 1 }
local spamThreads, spamON = {}, {}
local mainGui = nil
local actionBtnsGui = nil

function dragify(Frame)
    Frame.Active = true
    Frame.Selectable = true
    local UIS = game:GetService("UserInputService")
    local dragging, dragInput, dragStart, startPos

    Frame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = Frame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then dragging = false end
            end)
        end
    end)

    Frame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)

    UIS.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            Frame.Position = UDim2.new(
                startPos.X.Scale,
                startPos.X.Offset + delta.X,
                startPos.Y.Scale,
                startPos.Y.Offset + delta.Y
            )
        end
    end)
end

local function spammarComando(comando)
    spamON[comando] = true
    local delay = tonumber(delays[comando]) or 0.2
    if delay < 0.1 then delay = 0.1 end
    local repeticoes = tonumber(reps[comando]) or 1

    local channel = (TextChatService.TextChannels and TextChatService.TextChannels:FindFirstChild("RBXGeneral"))
        or (TextChatService:FindFirstChild("TextChannels") and TextChatService:FindFirstChild("TextChannels"):FindFirstChild("RBXGeneral"))
        or TextChatService:FindFirstChild("RBXGeneral")
        or TextChatService:FindFirstChild("General")
        or TextChatService:FindFirstChild("All")

    spamThreads[comando] = task.spawn(function()
        for i = 1, repeticoes do
            if channel and spamON[comando] then
                channel:SendAsync(comando)
            end
            task.wait(delay)
        end
        spamON[comando] = false
    end)
end

local function pararSpam(comando)
    spamON[comando] = false
end

local function showActionButtons()
    if actionBtnsGui then actionBtnsGui:Destroy() end
    actionBtnsGui = Instance.new("ScreenGui")
    actionBtnsGui.Parent = player:WaitForChild("PlayerGui")
    actionBtnsGui.Name = "ActionBtns"

    local cores = {
        [1] = Color3.fromRGB(45, 131, 246),
        [2] = Color3.fromRGB(254, 194, 47),
        [3] = Color3.fromRGB(82, 196, 125),
    }

    for i,cmd in ipairs(comandos) do
        local letra = iniciais[cmd]
        local btn = Instance.new("TextButton")
        btn.Parent = actionBtnsGui
        btn.Size = UDim2.new(0,56,0,56)
        btn.Position = UDim2.new(0, 36 + (i-1)*80, 1, -132)
        btn.BackgroundColor3 = cores[i]
        btn.BackgroundTransparency = 0.07
        btn.Text = letra
        btn.Font = Enum.Font.FredokaOne
        btn.TextSize = 38
        btn.TextColor3 = Color3.fromRGB(248,248,255)
        btn.Name = "Btn_"..cmd
        btn.Active = true
        btn.Selectable = true

        local corner = Instance.new("UICorner", btn)
        corner.CornerRadius = UDim.new(1,0)
        local stroke = Instance.new("UIStroke", btn)
        stroke.Color = Color3.fromRGB(23, 32, 42)
        stroke.Thickness = 2

        dragify(btn)
        btn.MouseButton1Click:Connect(function()
            btn.Text = "▶️"
            spammarComando(cmd)
            coroutine.wrap(function()
                while spamON[cmd] do
                    wait(0.1)
                end
                btn.Text = letra
            end)()
        end)
    end
end

local function hideActionButtons()
    if actionBtnsGui then actionBtnsGui:Destroy() actionBtnsGui = nil end
end

local function createMainGui()
    local gui = Instance.new("ScreenGui")
    gui.Name = "Spammar"
    gui.Parent = player:WaitForChild("PlayerGui")
    mainGui = gui

    local frame = Instance.new("Frame", gui)
    frame.Size = UDim2.new(0, 255, 0, 155 + #comandos*60)
    frame.Position = UDim2.new(0.5, -128, 0.5, -((155 + #comandos*60)//2))
    frame.BackgroundColor3 = Color3.fromRGB(33, 36, 55)
    frame.BackgroundTransparency = 0.07
    frame.Active = true
    frame.Selectable = true
    dragify(frame)
    local uicorner = Instance.new("UICorner", frame) uicorner.CornerRadius = UDim.new(0.17,0)
    local uistroke = Instance.new("UIStroke", frame)
    uistroke.Color = Color3.fromRGB(98,135,227)
    uistroke.Thickness = 3

    local titlebox = Instance.new("Frame", frame)
    titlebox.Size = UDim2.new(0.75,0,0,27)
    titlebox.Position = UDim2.new(0.125,0,0,12)
    titlebox.BackgroundColor3 = Color3.fromRGB(65,65,97)
    titlebox.BackgroundTransparency = 0.15
    local tcorner = Instance.new("UICorner", titlebox) tcorner.CornerRadius = UDim.new(0.52,0)
    local tstroke = Instance.new("UIStroke", titlebox)
    tstroke.Color = Color3.fromRGB(147, 168, 255)
    tstroke.Thickness = 1
    local titulo = Instance.new("TextLabel", titlebox)
    titulo.Text = "bobe7353i"
    titulo.Size = UDim2.new(1,0,1,0)
    titulo.Font = Enum.Font.FredokaOne
    titulo.TextSize = 18
    titulo.TextColor3 = Color3.fromRGB(243, 243, 255)
    titulo.BackgroundTransparency = 1

    for i,cmd in ipairs(comandos) do
        local yBase = 52 + (i-1)*62

        local lbl = Instance.new("TextLabel", frame)
        lbl.Text = cmd
        lbl.Size = UDim2.new(0.72,0,0,22)
        lbl.Position = UDim2.new(0.09,0,0,yBase+7)
        lbl.BackgroundTransparency = 1
        lbl.Font = Enum.Font.FredokaOne
        lbl.TextSize = 16
        lbl.TextColor3 = Color3.fromRGB(184,218,255)
        lbl.TextXAlignment = Enum.TextXAlignment.Left

        local delayBox = Instance.new("TextBox", frame)
        delayBox.Size = UDim2.new(0.4,0,0,19)
        delayBox.Position = UDim2.new(0.06,0,0,yBase+29)
        delayBox.Text = tostring(delays[cmd])
        delayBox.PlaceholderText = "⏱️ Delay"
        delayBox.Font = Enum.Font.GothamBold
        delayBox.TextSize = 13
        delayBox.BackgroundColor3 = Color3.fromRGB(27, 33, 61)
        delayBox.TextColor3 = Color3.fromRGB(217,217,255)
        local inputCorner1 = Instance.new("UICorner", delayBox)
        inputCorner1.CornerRadius = UDim.new(0.4,0)
        delayBox.FocusLost:Connect(function()
            delays[cmd] = tonumber(delayBox.Text) or 0.2
            if delays[cmd] < 0.1 then delays[cmd] = 0.1 end
            delayBox.Text = tostring(delays[cmd])
        end)

        local repsBox = Instance.new("TextBox", frame)
        repsBox.Size = UDim2.new(0.4,0,0,19)
        repsBox.Position = UDim2.new(0.53,0,0,yBase+29)
        repsBox.Text = tostring(reps[cmd])
        repsBox.PlaceholderText = "🔁 Repetições"
        repsBox.Font = Enum.Font.GothamBold
        repsBox.TextSize = 13
        repsBox.BackgroundColor3 = Color3.fromRGB(27, 33, 61)
        repsBox.TextColor3 = Color3.fromRGB(217,217,255)
        local inputCorner2 = Instance.new("UICorner", repsBox)
        inputCorner2.CornerRadius = UDim.new(0.4,0)
        repsBox.FocusLost:Connect(function()
            reps[cmd] = math.max(1, math.floor(tonumber(repsBox.Text) or 1))
            repsBox.Text = tostring(reps[cmd])
        end)

        local startBtn = Instance.new("TextButton", frame)
        startBtn.Text = "🚀 INICIAR"
        startBtn.Size = UDim2.new(0.4,0,0,18)
        startBtn.Position = UDim2.new(0.06,0,0,yBase+50)
        startBtn.BackgroundColor3 = Color3.fromRGB(86, 184, 123)
        startBtn.TextColor3 = Color3.fromRGB(250,250,250)
        startBtn.Font = Enum.Font.GothamBold
        startBtn.TextSize = 13
        local btnStartCorner = Instance.new("UICorner", startBtn)
        btnStartCorner.CornerRadius = UDim.new(0.5,0)
        startBtn.MouseButton1Click:Connect(function()
            pararSpam(cmd)
            spammarComando(cmd)
        end)

        local stopBtn = Instance.new("TextButton", frame)
        stopBtn.Text = "🛑 PARAR"
        stopBtn.Size = UDim2.new(0.4,0,0,18)
        stopBtn.Position = UDim2.new(0.53,0,0,yBase+50)
        stopBtn.BackgroundColor3 = Color3.fromRGB(205,81,103)
        stopBtn.TextColor3 = Color3.fromRGB(242, 240, 245)
        stopBtn.Font = Enum.Font.GothamBold
        stopBtn.TextSize = 13
        local btnStopCorner = Instance.new("UICorner", stopBtn)
        btnStopCorner.CornerRadius = UDim.new(0.5,0)
        stopBtn.MouseButton1Click:Connect(function() pararSpam(cmd) end)
    end

    local botaoAtivar = Instance.new("TextButton", frame)
    botaoAtivar.Text = "Ativar Botões"
    botaoAtivar.Size = UDim2.new(0.36,0,0,22)
    botaoAtivar.Position = UDim2.new(0.1,0,1,-32)
    botaoAtivar.BackgroundColor3 = Color3.fromRGB(67,168,236)
    botaoAtivar.TextColor3 = Color3.fromRGB(251, 251, 255)
    botaoAtivar.Font = Enum.Font.GothamBold
    botaoAtivar.TextSize = 13
    local bcorner = Instance.new("UICorner", botaoAtivar)
    bcorner.CornerRadius = UDim.new(0.5,0)
    botaoAtivar.MouseButton1Click:Connect(showActionButtons)

    local botaoDesativar = Instance.new("TextButton", frame)
    botaoDesativar.Text = "Desativar Botões"
    botaoDesativar.Size = UDim2.new(0.36,0,0,22)
    botaoDesativar.Position = UDim2.new(0.54,0,1,-32)
    botaoDesativar.BackgroundColor3 = Color3.fromRGB(136,54,200)
    botaoDesativar.TextColor3 = Color3.fromRGB(249,249,253)
    botaoDesativar.Font = Enum.Font.GothamBold
    botaoDesativar.TextSize = 13
    local dcorner = Instance.new("UICorner", botaoDesativar)
    dcorner.CornerRadius = UDim.new(0.5,0)
    botaoDesativar.MouseButton1Click:Connect(hideActionButtons)
end

-- ALTERADO: botão centralizado, com id novo e sem borda!
local function createMenuToggleBtn()
    local screenGui = Instance.new("ScreenGui")
    screenGui.Name = "MenuToggleBtn"
    screenGui.Parent = player:WaitForChild("PlayerGui")

    local btn = Instance.new("ImageButton", screenGui)
    btn.Size = UDim2.new(0, 44, 0, 44)
    btn.AnchorPoint = Vector2.new(0.5, 0.5)
    btn.Position = UDim2.new(0.5, 0, 0.5, 0)
    btn.BackgroundColor3 = Color3.fromRGB(28,28,43)
    btn.BackgroundTransparency = 0.05
    btn.Name = "ToggleMenuBtn"
    btn.Image = "rbxassetid://12052385551" -- Usando novo id
    local uicorner = Instance.new("UICorner", btn)
    uicorner.CornerRadius = UDim.new(1,0)
    -- NÃO tem mais UIStroke aqui

    dragify(btn)

    btn.MouseButton1Click:Connect(function()
        if mainGui then
            mainGui.Enabled = not mainGui.Enabled
        else
            createMainGui()
            mainGui.Enabled = true
        end
    end)
end

createMainGui()
createMenuToggleBtn()
mainGui.Enabled = false
