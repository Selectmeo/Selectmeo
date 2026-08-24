-- ============================================
-- BluezyGPT GUI Library — Safe Mode
-- ตัดทุกอย่างที่อาจ error ออก
-- ใช้ได้กับ executor ทุกตัว
-- ============================================

local Players = game:GetService("Players")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

-- Safe clamp
local function clamp(v, mn, mx)
    if v < mn then return mn end
    if v > mx then return mx end
    return v
end

-- Theme
local T = {
    bg = Color3.fromRGB(18, 18, 24),
    panel = Color3.fromRGB(24, 24, 32),
    elem = Color3.fromRGB(30, 30, 40),
    hover = Color3.fromRGB(36, 36, 48),
    accent = Color3.fromRGB(130, 80, 220),
    text = Color3.fromRGB(220, 220, 235),
    dim = Color3.fromRGB(140, 140, 160),
    accentTxt = Color3.fromRGB(180, 140, 255),
    border = Color3.fromRGB(45, 45, 60),
    success = Color3.fromRGB(60, 200, 120),
    danger = Color3.fromRGB(220, 70, 70),
}

-- Library
local G = {}
G.__index = G

function G.new(title, opts)
    opts = opts or {}
    local self = setmetatable({}, G)
    self.title = title or "BluezyGPT"
    self.w = opts.width or 500
    self.h = opts.height or 400
    self.pos = opts.position or UDim2.new(0.5, -250, 0.5, -200)
    self.key = opts.keybind or Enum.KeyCode.RightShift
    self.tabs = {}
    self.open = true
    self.elems = {}
    self:build()
    return self
end

function G:build()
    local sg = Instance.new("ScreenGui")
    sg.Name = "BZGUI"
    sg.ResetOnSpawn = false
    sg.Parent = LocalPlayer:WaitForChild("PlayerGui")
    self.sg = sg

    -- Main
    local mf = Instance.new("Frame")
    mf.Size = UDim2.new(0, self.w, 0, self.h)
    mf.Position = self.pos
    mf.BackgroundColor3 = T.bg
    mf.BorderSizePixel = 0
    mf.Parent = sg
    self.mf = mf
    Instance.new("UICorner", mf).CornerScale = UDim2.new(0, 10)

    -- Top bar
    local tb = Instance.new("Frame")
    tb.Size = UDim2.new(1, 0, 0, 32)
    tb.BackgroundColor3 = T.panel
    tb.BorderSizePixel = 0
    tb.Parent = mf
    Instance.new("UICorner", tb).CornerScale = UDim2.new(0, 10)

    -- Title
    local tl = Instance.new("TextLabel")
    tl.Size = UDim2.new(1, -50, 1, 0)
    tl.Position = UDim2.new(0, 8, 0, 0)
    tl.BackgroundTransparency = 1
    tl.Text = "⚡ " .. self.title
    tl.TextColor3 = T.text
    tl.TextSize = 13
    tl.Font = Enum.Font.GothamBold
    tl.TextXAlignment = Enum.TextXAlignment.Left
    tl.Parent = tb

    -- Close
    local cb = Instance.new("TextButton")
    cb.Size = UDim2.new(0, 28, 0, 28)
    cb.Position = UDim2.new(1, -32, 0, 2)
    cb.BackgroundColor3 = T.danger
    cb.Text = "✕"
    cb.TextColor3 = Color3.new(1,1,1)
    cb.TextSize = 13
    cb.Font = Enum.Font.GothamBold
    cb.Parent = tb
    Instance.new("UICorner", cb).CornerScale = UDim2.new(0, 5)
    cb.MouseButton1Click:Connect(function() self:toggle() end)

    -- Drag
    local drag = false
    local dx, dy
    tb.InputBegan:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
            drag = true
            dx = i.Position.X - mf.AbsolutePosition.X
            dy = i.Position.Y - mf.AbsolutePosition.Y
        end
    end)
    tb.InputChanged:Connect(function(i)
        if drag and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
            mf.Position = UDim2.new(0, i.Position.X - dx, 0, i.Position.Y - dy)
        end
    end)
    UserInputService.InputEnded:Connect(function(i)
        if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
            drag = false
        end
    end)

    -- Tab container
    local tc = Instance.new("Frame")
    tc.Size = UDim2.new(0, 140, 1, -32)
    tc.Position = UDim2.new(0, 0, 0, 32)
    tc.BackgroundColor3 = T.panel
    tc.BorderSizePixel = 0
    tc.Parent = mf
    self.tc = tc

    local tl2 = Instance.new("UIListLayout", tc)
    tl2.Padding = UDim.new(0, 2)
    tl2.SortOrder = Enum.SortOrder.LayoutOrder
    Instance.new("UIPadding", tc).PaddingTop = UDim.new(0, 4)
    Instance.new("UIPadding", tc).PaddingBottom = UDim.new(0, 4)
    Instance.new("UIPadding", tc).PaddingLeft = UDim.new(0, 4)
    Instance.new("UIPadding", tc).PaddingRight = UDim.new(0, 4)

    -- Content
    local cf = Instance.new("Frame")
    cf.Size = UDim2.new(1, -140, 1, -32)
    cf.Position = UDim2.new(0, 140, 0, 32)
    cf.BackgroundColor3 = T.bg
    cf.BorderSizePixel = 0
    cf.Parent = mf
    self.cf = cf

    local sf = Instance.new("ScrollingFrame")
    sf.Size = UDim2.new(1, 0, 1, 0)
    sf.BackgroundTransparency = 1
    sf.BorderSizePixel = 0
    sf.ScrollBarThickness = 4
    sf.AutomaticCanvasSize = Enum.AutomaticSize.Y
    sf.CanvasSize = UDim2.new(0, 0, 0, 0)
    sf.Parent = cf
    self.sf = sf

    local sl = Instance.new("UIListLayout", sf)
    sl.Padding = UDim.new(0, 5)
    Instance.new("UIPadding", sf).PaddingTop = UDim.new(0, 8)
    Instance.new("UIPadding", sf).PaddingBottom = UDim.new(0, 8)
    Instance.new("UIPadding", sf).PaddingLeft = UDim.new(0, 8)
    Instance.new("UIPadding", sf).PaddingRight = UDim.new(0, 8)

    -- Keybind
    UserInputService.InputBegan:Connect(function(i, gp)
        if not gp and i.KeyCode == self.key then
            self:toggle()
        end
    end)
end

function G:addTab(name, icon)
    local tab = {name = name, icon = icon or "📦", elems = {}, parent = self}
    table.insert(self.tabs, tab)
    self:makeTabBtn(tab, #self.tabs)
    if #self.tabs == 1 then self:selectTab(1) end
    return tab
end

function G:makeTabBtn(tab, idx)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, -8, 0, 32)
    btn.BackgroundColor3 = T.elem
    btn.Text = tab.icon .. " " .. tab.name
    btn.TextColor3 = T.dim
    btn.TextSize = 11
    btn.Font = Enum.Font.GothamBold
    btn.AutoButtonColor = false
    btn.Parent = self.tc
    Instance.new("UICorner", btn).CornerScale = UDim2.new(0, 5)

    btn.MouseButton1Click:Connect(function()
        self:selectTab(idx)
    end)
    tab.btn = btn
end

function G:selectTab(idx)
    for i, tab in ipairs(self.tabs) do
        tab.active = (i == idx)
        if tab.btn then
            if tab.active then
                tab.btn.BackgroundColor3 = T.accent
                tab.btn.TextColor3 = Color3.new(1,1,1)
            else
                tab.btn.BackgroundColor3 = T.elem
                tab.btn.TextColor3 = T.dim
            end
        end
    end

    for _, c in ipairs(self.sf:GetChildren()) do
        if c:IsA("Frame") or c:IsA("TextButton") or c:IsA("TextLabel") then
            c:Destroy()
        end
    end

    local tab = self.tabs[idx]
    if tab then
        for _, e in ipairs(tab.elems) do
            if e.render then e:render(self.sf) end
        end
    end
end

-- Toggle
function G:addToggle(name, def, cb)
    local e = {name = name, val = def or false, cb = cb or function() end}
    function e:render(p)
        local f = Instance.new("Frame")
        f.Size = UDim2.new(1, -16, 0, 28)
        f.BackgroundTransparency = 1
        f.Parent = p

        local tf = Instance.new("Frame")
        tf.Size = UDim2.new(0, 44, 0, 22)
        tf.Position = UDim2.new(1, -44, 0.5, -11)
        tf.BackgroundColor3 = T.elem
        tf.BorderSizePixel = 0
        tf.Parent = f
        Instance.new("UICorner", tf).CornerScale = UDim2.new(0, 11)

        local cir = Instance.new("Frame")
        cir.Size = UDim2.new(0, 16, 0, 16)
        cir.Position = UDim2.new(0, 3, 0, 3)
        cir.BackgroundColor3 = T.dim
        cir.BorderSizePixel = 0
        cir.Parent = tf
        Instance.new("UICorner", cir).CornerScale = UDim2.new(0, 8)

        local lbl = Instance.new("TextLabel")
        lbl.Size = UDim2.new(1, -52, 1, 0)
        lbl.BackgroundTransparency = 1
        lbl.Text = e.name
        lbl.TextColor3 = T.text
        lbl.TextSize = 12
        lbl.Font = Enum.Font.Gotham
        lbl.TextXAlignment = Enum.TextXAlignment.Left
        lbl.Parent = f

        local function upd()
            local pos = e.val and UDim2.new(0, 25, 0, 3) or UDim2.new(0, 3, 0, 3)
            local col = e.val and T.success or T.dim
            local bg = e.val and T.accent or T.elem
            pcall(function()
                TweenService:Create(cir, TweenInfo.new(0.15), {Position = pos, BackgroundColor3 = col}):Play()
                TweenService:Create(tf, TweenInfo.new(0.15), {BackgroundColor3 = bg}):Play()
            end)
            e.cb(e.val)
        end

        tf.InputBegan:Connect(function(i)
            if i.UserInputType == Enum.UserInputType.MouseButton1 then
                e.val = not e.val
                upd()
            end
        end)
        if e.val then upd() end
    end
    table.insert(self.elems, e)
    return e
end

-- Button
function G:addButton(name, cb)
    local e = {name = name, cb = cb or function() end}
    function e:render(p)
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(1, -16, 0, 30)
        btn.BackgroundColor3 = T.elem
        btn.Text = e.name
        btn.TextColor3 = T.text
        btn.TextSize = 12
        btn.Font = Enum.Font.GothamBold
        btn.AutoButtonColor = false
        btn.Parent = p
        Instance.new("UICorner", btn).CornerScale = UDim2.new(0, 5)

        btn.MouseButton1Click:Connect(function()
            pcall(function()
                TweenService:Create(btn, TweenInfo.new(0.05), {Size = UDim2.new(1, -20, 0, 28)}):Play()
                wait(0.05)
                TweenService:Create(btn, TweenInfo.new(0.1), {Size = UDim2.new(1, -16, 0, 30)}):Play()
            end)
            e.cb()
        end)
    end
    table.insert(self.elems, e)
    return e
end

-- Slider
function G:addSlider(name, mn, mx, def, suf, cb)
    local e = {name = name, mn = mn or 0, mx = mx or 100, val = def or mn or 0, suf = suf or "", cb = cb or function() end}
    function e:render(p)
        local f = Instance.new("Frame")
        f.Size = UDim2.new(1, -16, 0, 46)
        f.BackgroundTransparency = 1
        f.Parent = p

        local lbl = Instance.new("TextLabel")
        lbl.Size = UDim2.new(1, 0, 0, 16)
        lbl.BackgroundTransparency = 1
        lbl.Text = e.name
        lbl.TextColor3 = T.text
        lbl.TextSize = 11
        lbl.Font = Enum.Font.Gotham
        lbl.TextXAlignment = Enum.TextXAlignment.Left
        lbl.Parent = f

        local trk = Instance.new("Frame")
        trk.Size = UDim2.new(1, 0, 0, 12)
        trk.Position = UDim2.new(0, 0, 0, 22)
        trk.BackgroundColor3 = T.elem
        trk.BorderSizePixel = 0
        trk.Parent = f
        Instance.new("UICorner", trk).CornerScale = UDim2.new(0, 6)

        local fl = Instance.new("Frame")
        fl.Size = UDim2.new(0, 0, 1, 0)
        fl.BackgroundColor3 = T.accent
        fl.BorderSizePixel = 0
        fl.Parent = trk
        Instance.new("UICorner", fl).CornerScale = UDim2.new(0, 6)

        local vl = Instance.new("TextLabel")
        vl.Size = UDim2.new(0, 56, 0, 14)
        vl.Position = UDim2.new(1, -56, 0, 22)
        vl.BackgroundTransparency = 1
        vl.Text = tostring(e.val) .. e.suf
        vl.TextColor3 = T.accentTxt
        vl.TextSize = 10
        vl.Font = Enum.Font.GothamBold
        vl.TextXAlignment = Enum.TextXAlignment.Right
        vl.Parent = f

        local dg = false
        trk.InputBegan:Connect(function(i)
            if i.UserInputType == Enum.UserInputType.MouseButton1 then
                dg = true
                local rx = clamp((i.Position.X - trk.AbsolutePosition.X) / trk.AbsoluteSize.X, 0, 1)
                e.val = math.floor(e.mn + (e.mx - e.mn) * rx + 0.5)
                fl.Size = UDim2.new(rx, 0, 1, 0)
                vl.Text = tostring(e.val) .. e.suf
                e.cb(e.val)
            end
        end)
        trk.InputChanged:Connect(function(i)
            if dg and i.UserInputType == Enum.UserInputType.MouseMovement then
                local rx = clamp((i.Position.X - trk.AbsolutePosition.X) / trk.AbsoluteSize.X, 0, 1)
                e.val = math.floor(e.mn + (e.mx - e.mn) * rx + 0.5)
                fl.Size = UDim2.new(rx, 0, 1, 0)
                vl.Text = tostring(e.val) .. e.suf
                e.cb(e.val)
            end
        end)
        UserInputService.InputEnded:Connect(function(i)
            if i.UserInputType == Enum.UserInputType.MouseButton1 then dg = false end
        end)

        -- Init
        local ip = (e.val - e.mn) / (e.mx - e.mn)
        fl.Size = UDim2.new(ip, 0, 1, 0)
        vl.Text = tostring(e.val) .. e.suf
    end
    table.insert(self.elems, e)
    return e
end

-- Textbox
function G:addTextbox(name, ph, cb)
    local e = {name = name, ph = ph or "...", cb = cb or function() end}
    function e:render(p)
        local f = Instance.new("Frame")
        f.Size = UDim2.new(1, -16, 0, 50)
        f.BackgroundTransparency = 1
        f.Parent = p

        local lbl = Instance.new("TextLabel")
        lbl.Size = UDim2.new(1, 0, 0, 14)
        lbl.BackgroundTransparency = 1
        lbl.Text = e.name
        lbl.TextColor3 = T.text
        lbl.TextSize = 11
        lbl.Font = Enum.Font.Gotham
        lbl.TextXAlignment = Enum.TextXAlignment.Left
        lbl.Parent = f

        local box = Instance.new("TextBox")
        box.Size = UDim2.new(1, 0, 0, 26)
        box.Position = UDim2.new(0, 0, 0, 18)
        box.BackgroundColor3 = T.elem
        box.Text = ""
        box.PlaceholderText = e.ph
        box.PlaceholderColor3 = T.dim
        box.TextColor3 = T.text
        box.TextSize = 12
        box.Font = Enum.Font.Gotham
        box.ClearTextOnFocus = false
        box.Parent = f
        Instance.new("UICorner", box).CornerScale = UDim2.new(0, 5)
        Instance.new("UIPadding", box).PaddingLeft = UDim.new(0, 6)

        box.FocusLost:Connect(function(ep)
            if ep then e.cb(box.Text) end
        end)
    end
    table.insert(self.elems, e)
    return e
end

-- Label
function G:addLabel(text, col)
    local e = {text = text, col = col or T.dim}
    function e:render(p)
        local lbl = Instance.new("TextLabel")
        lbl.Size = UDim2.new(1, -16, 0, 16)
        lbl.BackgroundTransparency = 1
        lbl.Text = text
        lbl.TextColor3 = self.col
        lbl.TextSize = 11
        lbl.Font = Enum.Font.Gotham
        lbl.TextXAlignment = Enum.TextXAlignment.Left
        lbl.TextWrapped = true
        lbl.Parent = p
    end
    table.insert(self.elems, e)
    return e
end

-- Dropdown
function G:addDropdown(name, opts, def, cb)
    local e = {name = name, opts = opts or {}, val = def or "", cb = cb or function() end, open = false}
    function e:render(p)
        local f = Instance.new("Frame")
        f.Size = UDim2.new(1, -16, 0, 28)
        f.BackgroundTransparency = 1
        f.Parent = p

        local lbl = Instance.new("TextLabel")
        lbl.Size = UDim2.new(1, 0, 0, 14)
        lbl.BackgroundTransparency = 1
        lbl.Text = e.name
        lbl.TextColor3 = T.text
        lbl.TextSize = 11
        lbl.Font = Enum.Font.Gotham
        lbl.TextXAlignment = Enum.TextXAlignment.Left
        lbl.Parent = f

        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(1, 0, 0, 26)
        btn.Position = UDim2.new(0, 0, 0, 16)
        btn.BackgroundColor3 = T.elem
        btn.Text = " " .. e.val
        btn.TextColor3 = T.text
        btn.TextSize = 11
        btn.Font = Enum.Font.Gotham
        btn.TextXAlignment = Enum.TextXAlignment.Left
        btn.Parent = f
        Instance.new("UICorner", btn).CornerScale = UDim2.new(0, 5)

        local lst = Instance.new("Frame")
        lst.Size = UDim2.new(1, 0, 0, 0)
        lst.Position = UDim2.new(0, 0, 0, 44)
        lst.BackgroundColor3 = T.panel
        lst.BorderSizePixel = 0
        lst.ClipsDescendants = true
        lst.Parent = f
        Instance.new("UICorner", lst).CornerScale = UDim2.new(0, 5)
        Instance.new("UIListLayout", lst).Padding = UDim.new(0, 1)

        local function build()
            for _, c in ipairs(lst:GetChildren()) do
                if c:IsA("TextButton") then c:Destroy() end
            end
            for _, o in ipairs(e.opts) do
                local ob = Instance.new("TextButton")
                ob.Size = UDim2.new(1, 0, 0, 24)
                ob.BackgroundColor3 = T.elem
                ob.Text = " " .. o
                ob.TextColor3 = T.text
                ob.TextSize = 10
                ob.Font = Enum.Font.Gotham
                ob.TextXAlignment = Enum.TextXAlignment.Left
                ob.Parent = lst
                ob.MouseButton1Click:Connect(function()
                    e.val = o
                    btn.Text = " " .. o
                    e.cb(o)
                    e.open = false
                    pcall(function() TweenService:Create(lst, TweenInfo.new(0.15), {Size = UDim2.new(1, 0, 0, 0)}):Play() end)
                end)
            end
        end

        btn.MouseButton1Click:Connect(function()
            e.open = not e.open
            if e.open then
                build()
                local h = math.min(#e.opts * 24, 120)
                lst.Size = UDim2.new(1, 0, 0, h)
            else
                pcall(function() TweenService:Create(lst, TweenInfo.new(0.15), {Size = UDim2.new(1, 0, 0, 0)}):Play() end)
            end
        end)
    end
    table.insert(self.elems, e)
    return e
end

-- Section
function G:addSection(name)
    local e = {name = name}
    function e:render(p)
        local f = Instance.new("Frame")
        f.Size = UDim2.new(1, -16, 0, 32)
        f.BackgroundTransparency = 1
        f.Parent = p

        local line = Instance.new("Frame")
        line.Size = UDim2.new(1, 0, 0, 1)
        line.BackgroundColor3 = T.border
        line.Position = UDim2.new(0, 0, 0.5, 0)
        line.Parent = f

        local lbl = Instance.new("TextLabel")
        lbl.Size = UDim2.new(0, 200, 0, 14)
        lbl.Position = UDim2.new(0, 6, 0, -7)
        lbl.BackgroundTransparency = 1
        lbl.Text = "◆ " .. e.name
        lbl.TextColor3 = T.accentTxt
        lbl.TextSize = 10
        lbl.Font = Enum.Font.GothamBold
        lbl.TextXAlignment = Enum.TextXAlignment.Left
        lbl.Parent = f
    end
    table.insert(self.elems, e)
    return e
end

-- Toggle visibility
function G:toggle()
    self.open = not self.open
    self.mf.Visible = self.open
end

-- Destroy
function G:destroy()
    if self.sg then self.sg:Destroy() end
end

return G
