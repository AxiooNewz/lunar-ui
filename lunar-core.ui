-- language: Lua/Luau
-- LunarCore UI Library (Rayfield-Compatible)

local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local Library = {
    Window = nil,
    Options = {},
    Flags = {},
    Notifications = {}
}

-- // Theme //
local COLOR = {
    Void = Color3.fromRGB(8, 8, 16),
    Card = Color3.fromRGB(12, 12, 24),
    Card2 = Color3.fromRGB(22, 22, 40),
    Accent = Color3.fromRGB(110, 70, 255),
    Accent2 = Color3.fromRGB(0, 200, 255),
    Text = Color3.fromRGB(240, 240, 255),
    Sub = Color3.fromRGB(140, 150, 190)
}

local function Make(class, props, parent)
    local obj = Instance.new(class)
    for k, v in pairs(props or {}) do obj[k] = v end
    if parent then obj.Parent = parent end
    return obj
end

local function Corner(p, r) return Make("UICorner", {CornerRadius = UDim.new(0, r)}, p) end
local function Stroke(p, c, t, tr) return Make("UIStroke", {Color = c or COLOR.Accent, Thickness = t or 1, Transparency = tr or 0.4}, p) end
local function Tween(o, t, p, s) local tw = TweenService:Create(o, TweenInfo.new(t, s or Enum.EasingStyle.Quad, Enum.EasingDirection.Out), p); tw:Play(); return tw end

-- // Dragging System //
local function MakeDraggable(frame, dragFrame)
    local dragging, dragInput, dragStart, startPos
    dragFrame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = frame.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then dragging = false end
            end)
        end
    end)
    dragFrame.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            frame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end

-- // Notifications //
function Library:Notify(title, text, duration, type)
    local gui = game:GetService("CoreGui"):FindFirstChild("LunarNotifyGui")
    if not gui then
        gui = Make("ScreenGui", {Name = "LunarNotifyGui", ResetOnSpawn = false, DisplayOrder = 999}, (gethui and gethui()) or game:GetService("CoreGui"))
    end
    
    local frame = Make("Frame", {Size = UDim2.fromOffset(250, 60), Position = UDim2.new(1, -270, 0, 20 + (#self.Notifications * 70)), BackgroundColor3 = COLOR.Card}, gui)
    Corner(frame, 8); Stroke(frame, COLOR.Accent, 1, 0.2)
    table.insert(self.Notifications, frame)

    Make("TextLabel", {Size = UDim2.new(1, -20, 0, 20), Position = UDim2.fromOffset(10, 8), BackgroundTransparency = 1, Text = title, TextColor3 = COLOR.Accent, Font = Enum.Font.GothamBold, TextSize = 14, TextXAlignment = Enum.TextXAlignment.Left}, frame)
    Make("TextLabel", {Size = UDim2.new(1, -20, 0, 30), Position = UDim2.fromOffset(10, 26), BackgroundTransparency = 1, Text = text, TextColor3 = COLOR.Text, Font = Enum.Font.Gotham, TextSize = 12, TextWrapped = true, TextXAlignment = Enum.TextXAlignment.Left}, frame)

    Tween(frame, 0.3, {Position = UDim2.new(1, -270, 0, 20 + (#self.Notifications * 70))})
    
    task.delay(duration or 4, function()
        Tween(frame, 0.3, {Position = UDim2.new(1, 100, 0, 20)})
        task.wait(0.4)
        for i, v in ipairs(self.Notifications) do
            if v == frame then table.remove(self.Notifications, i) break end
        end
        frame:Destroy()
    end)
end

-- // Window Creation //
function Library:CreateWindow(config)
    local ScreenGui = Make("ScreenGui", {Name = "LunarCoreUI", ResetOnSpawn = false, DisplayOrder = 999}, (gethui and gethui()) or game:GetService("CoreGui"))
    local Main = Make("Frame", {Size = UDim2.fromOffset(600, 400), Position = UDim2.fromScale(0.5, 0.5), AnchorPoint = Vector2.new(0.5, 0.5), BackgroundColor3 = COLOR.Void, Visible = false}, ScreenGui)
    Corner(Main, 12); Stroke(Main, COLOR.Accent, 1, 0.5)
    
    local Topbar = Make("Frame", {Size = UDim2.new(1, 0, 0, 40), BackgroundColor3 = COLOR.Card, BackgroundTransparency = 0.2}, Main)
    Corner(Topbar, 12)
    Make("Frame", {Size = UDim2.new(1, 0, 0, 15), Position = UDim2.new(0, 0, 1, -15), BackgroundColor3 = COLOR.Card, BorderSizePixel = 0}, Topbar) -- Corner fix
    Make("TextLabel", {Size = UDim2.new(1, -80, 1, 0), Position = UDim2.fromOffset(15, 0), BackgroundTransparency = 1, Text = config.Title .. " | " .. config.SubTitle, TextColor3 = COLOR.Text, Font = Enum.Font.GothamBold, TextSize = 14, TextXAlignment = Enum.TextXAlignment.Left}, Topbar)
    
    local Close = Make("TextButton", {Size = UDim2.fromOffset(25, 25), Position = UDim2.new(1, -35, 0.5, -12.5), BackgroundColor3 = Color3.fromRGB(255, 100, 100), Text = "X", TextColor3 = Color3.new(1,1,1), Font = Enum.Font.GothamBold, TextSize = 12, AutoButtonColor = false}, Topbar)
    Corner(Close, 6)
    Close.MouseButton1Click:Connect(function() Main.Visible = false end)

    MakeDraggable(Main, Topbar)

    local Sidebar = Make("Frame", {Size = UDim2.new(0, 150, 1, -40), Position = UDim2.new(0, 0, 0, 40), BackgroundColor3 = COLOR.Card, BackgroundTransparency = 0.5}, Main)
    Make("Frame", {Size = UDim2.new(0, 1, 1, 0), Position = UDim2.new(1, 0, 0, 0), BackgroundColor3 = COLOR.Accent, BackgroundTransparency = 0.8}, Sidebar) -- Divider

    local Content = Make("Frame", {Size = UDim2.new(1, -150, 1, -40), Position = UDim2.new(0, 150, 0, 40), BackgroundTransparency = 1}, Main)

    local Window = {Tabs = {}, ActiveTab = nil, Sidebar = Sidebar, Content = Content}

    function Window:AddTab(name, icon)
        local TabBtn = Make("TextButton", {Size = UDim2.new(1, -10, 0, 32), Position = UDim2.fromOffset(5, 5 + (#self.Tabs * 36)), BackgroundColor3 = COLOR.Card2, BackgroundTransparency = 1, Text = name, TextColor3 = COLOR.Sub, Font = Enum.Font.GothamMedium, TextSize = 13, AutoButtonColor = false}, self.Sidebar)
        Corner(TabBtn, 6)

        local TabFrame = Make("ScrollingFrame", {Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Visible = false, ScrollBarThickness = 2, ScrollBarImageColor3 = COLOR.Accent, CanvasSize = UDim2.new(0,0,0,0), AutomaticCanvasSize = Enum.AutomaticSize.Y}, self.Content)
        
        local TabData = {Button = TabBtn, Frame = TabFrame, Groups = 0}
        table.insert(self.Tabs, TabData)
        
        TabBtn.MouseButton1Click:Connect(function()
            for _, t in ipairs(self.Tabs) do
                t.Button.BackgroundTransparency = 1
                t.Button.TextColor3 = COLOR.Sub
                t.Frame.Visible = false
            end
            TabBtn.BackgroundTransparency = 0.8
            TabBtn.TextColor3 = COLOR.Accent
            TabFrame.Visible = true
            self.ActiveTab = TabData
        end)

        if #self.Tabs == 1 then TabBtn:Fire("MouseButton1Click") end -- Auto open first tab

        function TabData:AddLeftGroupbox(title, icon)
            local Group = Make("Frame", {Size = UDim2.new(0.5, -10, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, Position = UDim2.new(0, 5, 0, 5), BackgroundColor3 = COLOR.Card, BackgroundTransparency = 0.3}, TabFrame)
            Corner(Group, 8); Stroke(Group, COLOR.Accent, 1, 0.8)
            local Pad = Make("UIPadding", {PaddingTop = UDim.new(0, 10), PaddingBottom = UDim.new(0, 10), PaddingLeft = UDim.new(0, 10), PaddingRight = UDim.new(0, 10)}, Group)
            Make("UIListLayout", {SortOrder = Enum.SortOrder.LayoutOrder, Padding = UDim.new(0, 8)}, Group)
            Make("TextLabel", {Size = UDim2.new(1, 0, 0, 20), BackgroundTransparency = 1, Text = title, TextColor3 = COLOR.Accent, Font = Enum.Font.GothamBold, TextSize = 12, TextXAlignment = Enum.TextXAlignment.Left, LayoutOrder = 0}, Group)

            local Groupbox = {UI = Group, Index = 0}
            self:_SetupGroupbox(Groupbox)
            return Groupbox
        end

        function TabData:AddRightGroupbox(title, icon)
            local Group = Make("Frame", {Size = UDim2.new(0.5, -10, 0, 0), AutomaticSize = Enum.AutomaticSize.Y, Position = UDim2.new(0.5, 5, 0, 5), BackgroundColor3 = COLOR.Card, BackgroundTransparency = 0.3}, TabFrame)
            Corner(Group, 8); Stroke(Group, COLOR.Accent, 1, 0.8)
            Make("UIPadding", {PaddingTop = UDim.new(0, 10), PaddingBottom = UDim.new(0, 10), PaddingLeft = UDim.new(0, 10), PaddingRight = UDim.new(0, 10)}, Group)
            Make("UIListLayout", {SortOrder = Enum.SortOrder.LayoutOrder, Padding = UDim.new(0, 8)}, Group)
            Make("TextLabel", {Size = UDim2.new(1, 0, 0, 20), BackgroundTransparency = 1, Text = title, TextColor3 = COLOR.Accent, Font = Enum.Font.GothamBold, TextSize = 12, TextXAlignment = Enum.TextXAlignment.Left, LayoutOrder = 0}, Group)

            local Groupbox = {UI = Group, Index = 0}
            self:_SetupGroupbox(Groupbox)
            return Groupbox
        end
        return TabData
    end

    function Window:AddTabSection(name) end -- Ignored in Lite

    Main.Visible = true
    self.Window = Window
    return Window
end

-- // Groupbox Elements Builder //
function Library:_SetupGroupbox(Groupbox)
    local function NextIndex() Groupbox.Index = Groupbox.Index + 1 return Groupbox.Index end

    function Groupbox:AddLabel(text, config)
        Make("TextLabel", {Size = UDim2.new(1, 0, 0, 20), BackgroundTransparency = 1, Text = text, TextColor3 = COLOR.Sub, Font = Enum.Font.Gotham, TextSize = 12, TextXAlignment = Enum.TextXAlignment.Left, LayoutOrder = NextIndex()}, self.UI)
    end

    function Groupbox:AddButton(config)
        local Btn = Make("TextButton", {Size = UDim2.new(1, 0, 0, 30), BackgroundColor3 = COLOR.Card2, Text = config.Text, TextColor3 = COLOR.Text, Font = Enum.Font.GothamBold, TextSize = 12, AutoButtonColor = false, LayoutOrder = NextIndex()}, self.UI)
        Corner(Btn, 6); Stroke(Btn, COLOR.Accent, 1, 0.6)
        Btn.MouseButton1Click:Connect(function() if config.Func then task.spawn(config.Func) end end)
    end

    function Groupbox:AddToggle(name, config)
        local Frame = Make("Frame", {Size = UDim2.new(1, 0, 0, 30), BackgroundColor3 = COLOR.Card2, LayoutOrder = NextIndex()}, self.UI)
        Corner(Frame, 6); Stroke(Frame, COLOR.Accent, 1, 0.8)
        
        Make("TextLabel", {Size = UDim2.new(1, -50, 1, 0), Position = UDim2.fromOffset(10, 0), BackgroundTransparency = 1, Text = config.Text, TextColor3 = COLOR.Text, Font = Enum.Font.Gotham, TextSize = 12, TextXAlignment = Enum.TextXAlignment.Left}, Frame)
        
        local Track = Make("Frame", {Size = UDim2.fromOffset(36, 18), Position = UDim2.new(1, -46, 0.5, -9), BackgroundColor3 = Color3.fromRGB(50, 45, 80)}, Frame)
        Corner(Track, 999)
        local Knob = Make("Frame", {Size = UDim2.fromOffset(14, 14), Position = UDim2.fromOffset(2, 2), BackgroundColor3 = COLOR.Text}, Track)
        Corner(Knob, 999)

        local ToggleObj = {Value = config.Default or false}
        
        local function Render(anim)
            local on = ToggleObj.Value
            local tC = on and COLOR.Accent or Color3.fromRGB(50, 45, 80)
            local kP = UDim2.fromOffset(on and 20 or 2, 2)
            if anim then Tween(Track, 0.2, {BackgroundColor3 = tC}); Tween(Knob, 0.2, {Position = kP})
            else Track.BackgroundColor3 = tC; Knob.Position = kP end
        end
        Render(false)

        local Btn = Make("TextButton", {Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Text = ""}, Frame)
        Btn.MouseButton1Click:Connect(function()
            ToggleObj.Value = not ToggleObj.Value
            Render(true)
            if config.Callback then task.spawn(config.Callback, ToggleObj.Value) end
        end)

        function ToggleObj:SetValue(val)
            ToggleObj.Value = val
            Render(true)
            if config.Callback then task.spawn(config.Callback, val) end
        end
        function ToggleObj:OnChanged(fn) end -- Stub for compatibility

        Library.Options[name] = ToggleObj
        return ToggleObj
    end

    function Groupbox:AddSlider(name, config)
        local Frame = Make("Frame", {Size = UDim2.new(1, 0, 0, 40), BackgroundColor3 = COLOR.Card2, LayoutOrder = NextIndex()}, self.UI)
        Corner(Frame, 6); Stroke(Frame, COLOR.Accent, 1, 0.8)
        
        local Lbl = Make("TextLabel", {Size = UDim2.new(1, -20, 0, 20), Position = UDim2.fromOffset(10, 2), BackgroundTransparency = 1, Text = config.Text .. ": " .. tostring(config.Default), TextColor3 = COLOR.Text, Font = Enum.Font.Gotham, TextSize = 12, TextXAlignment = Enum.TextXAlignment.Left}, Frame)
        
        local Track = Make("Frame", {Size = UDim2.new(1, -20, 0, 4), Position = UDim2.new(0, 10, 1, -12), BackgroundColor3 = Color3.fromRGB(45, 45, 58)}, Frame)
        Corner(Track, 2)
        local Fill = Make("Frame", {Size = UDim2.new((config.Default - config.Min)/(config.Max - config.Min), 0, 1, 0), BackgroundColor3 = COLOR.Accent}, Track)
        Corner(Fill, 2)

        local SliderObj = {Value = config.Default}
        local dragging = false

        local Btn = Make("TextButton", {Size = UDim2.fromScale(1, 1), BackgroundTransparency = 1, Text = ""}, Track)
        
        local function Update(x)
            local rel = math.clamp((x - Track.AbsolutePosition.X) / Track.AbsoluteSize.X, 0, 1)
            local val = math.floor(config.Min + (config.Max - config.Min) * rel)
            Fill.Size = UDim2.new(rel, 0, 1, 0)
            Lbl.Text = config.Text .. ": " .. tostring(val)
            SliderObj.Value = val
            if config.Callback then task.spawn(config.Callback, val) end
        end

        Track.InputBegan:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then dragging = true; Update(input.Position.X) end end)
        UserInputService.InputChanged:Connect(function(input) if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then Update(input.Position.X) end end)
        UserInputService.InputEnded:Connect(function(input) if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then dragging = false end end)

        Library.Options[name] = SliderObj
        return SliderObj
    end

    function Groupbox:AddDropdown(name, config)
        local Frame = Make("Frame", {Size = UDim2.new(1, 0, 0, 30), BackgroundColor3 = COLOR.Card2, ClipsDescendants = true, LayoutOrder = NextIndex()}, self.UI)
        Corner(Frame, 6); Stroke(Frame, COLOR.Accent, 1, 0.8)

        local Header = Make("TextButton", {Size = UDim2.new(1, 0, 0, 30), BackgroundTransparency = 1, Text = "", AutoButtonColor = false}, Frame)
        Make("TextLabel", {Size = UDim2.new(1, -20, 1, 0), Position = UDim2.fromOffset(10, 0), BackgroundTransparency = 1, Text = config.Text .. ": " .. tostring(config.Default), TextColor3 = COLOR.Text, Font = Enum.Font.Gotham, TextSize = 12, TextXAlignment = Enum.TextXAlignment.Left}, Header)

        local List = Make("Frame", {Size = UDim2.new(1, 0, 0, 0), Position = UDim2.fromOffset(0, 30), BackgroundColor3 = COLOR.Card, AutomaticSize = Enum.AutomaticSize.Y}, Frame)
        
        local DropdownObj = {Value = config.Default}
        local expanded = false

        Header.MouseButton1Click:Connect(function()
            expanded = not expanded
            Frame.Size = expanded and UDim2.new(1, 0, 0, 30 + (#config.Values * 25)) or UDim2.new(1, 0, 0, 30)
        end)

        for _, val in ipairs(config.Values) do
            local Btn = Make("TextButton", {Size = UDim2.new(1, 0, 0, 25), BackgroundTransparency = 1, Text = tostring(val), TextColor3 = COLOR.Sub, Font = Enum.Font.Gotham, TextSize = 12, AutoButtonColor = false}, List)
            Btn.MouseButton1Click:Connect(function()
                DropdownObj.Value = val
                Header.TextLabel.Text = config.Text .. ": " .. tostring(val)
                Frame.Size = UDim2.new(1, 0, 0, 30)
                expanded = false
                if config.Callback then task.spawn(config.Callback, val) end
            end)
        end
        
        Library.Options[name] = DropdownObj
        return DropdownObj
    end

    function Groupbox:AddInput(name, config)
        local Frame = Make("Frame", {Size = UDim2.new(1, 0, 0, 30), BackgroundColor3 = COLOR.Card2, LayoutOrder = NextIndex()}, self.UI)
        Corner(Frame, 6); Stroke(Frame, COLOR.Accent, 1, 0.8)
        local Box = Make("TextBox", {Size = UDim2.new(1, -20, 1, 0), Position = UDim2.fromOffset(10, 0), BackgroundTransparency = 1, Text = config.Default or "", PlaceholderText = config.Placeholder or "...", TextColor3 = COLOR.Text, Font = Enum.Font.Gotham, TextSize = 12, TextXAlignment = Enum.TextXAlignment.Left}, Frame)
        Box.FocusLost:Connect(function()
            if config.Callback then task.spawn(config.Callback, Box.Text) end
        end)
        Library.Options[name] = {Value = config.Default}
    end
end

function Library:Unload()
    local gui = game:GetService("CoreGui"):FindFirstChild("LunarCoreUI")
    if gui then gui:Destroy() end
end

return Library
