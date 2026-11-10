-- Anime Adventures Hub (compact)
local P=game:GetService("Players").LocalPlayer
local RS=game:GetService("ReplicatedStorage")
local UIS=game:GetService("UserInputService")

-- UI
local g=Instance.new("ScreenGui",P:WaitForChild("PlayerGui"))
g.Name="AAHub"; g.ResetOnSpawn=false
local m=Instance.new("Frame",g)
m.Size=UDim2.new(0,480,0,340); m.Position=UDim2.new(.5,-240,.5,-170)
m.BackgroundColor3=Color3.fromRGB(20,20,30); m.Active=true; m.Draggable=true
Instance.new("UICorner",m).CornerRadius=UDim.new(0,10)
local tb=Instance.new("Frame",m)
tb.Size=UDim2.new(1,0,0,40); tb.BackgroundColor3=Color3.fromRGB(160,30,60)
Instance.new("UICorner",tb).CornerRadius=UDim.new(0,10)
local tl=Instance.new("TextLabel",tb)
tl.Size=UDim2.new(1,-60,1,0); tl.Position=UDim2.new(0,15,0,0)
tl.BackgroundTransparency=1; tl.Text="⚔️ AA Hub"; tl.TextColor3=Color3.new(1,1,1)
tl.Font=Enum.Font.GothamBold; tl.TextSize=16; tl.TextXAlignment=Enum.TextXAlignment.Left
local cb=Instance.new("TextButton",tb)
cb.Size=UDim2.new(0,28,0,28); cb.Position=UDim2.new(1,-34,0,6)
cb.BackgroundColor3=Color3.fromRGB(200,50,70); cb.Text="X"; cb.TextColor3=Color3.new(1,1,1)
cb.Font=Enum.Font.GothamBold
Instance.new("UICorner",cb).CornerRadius=UDim.new(0,6)
local ct=Instance.new("ScrollingFrame",m)
ct.Size=UDim2.new(1,-20,1,-55); ct.Position=UDim2.new(0,10,0,45)
ct.BackgroundColor3=Color3.fromRGB(30,30,42); ct.BorderSizePixel=0; ct.ScrollBarThickness=4
Instance.new("UICorner",ct).CornerRadius=UDim.new(0,8)
Instance.new("UIListLayout",ct).Padding=UDim.new(0,6)
Instance.new("UIPadding",ct).PaddingTop=UDim.new(0,8)

local function sec(t)
    local f=Instance.new("Frame",ct); f.Size=UDim2.new(1,-16,0,0)
    f.AutomaticSize=Enum.AutomaticSize.Y; f.BackgroundColor3=Color3.fromRGB(40,40,55)
    Instance.new("UICorner",f).CornerRadius=UDim.new(0,6)
    Instance.new("UIListLayout",f).Padding=UDim.new(0,4)
    Instance.new("UIPadding",f).PaddingTop=UDim.new(0,6)
    Instance.new("UIPadding",f).PaddingLeft=UDim.new(0,8)
    Instance.new("UIPadding",f).PaddingRight=UDim.new(0,8)
    Instance.new("UIPadding",f).PaddingBottom=UDim.new(0,6)
    local l=Instance.new("TextLabel",f); l.Size=UDim2.new(1,0,0,20)
    l.BackgroundTransparency=1; l.Text=t; l.TextColor3=Color3.fromRGB(240,180,200)
    l.Font=Enum.Font.GothamBold; l.TextSize=13; l.TextXAlignment=Enum.TextXAlignment.Left
    return f
end

local function tog(p,t,d,cbk)
    local f=Instance.new("Frame",p); f.Size=UDim2.new(1,0,0,26); f.BackgroundTransparency=1
    local l=Instance.new("TextLabel",f); l.Size=UDim2.new(.7,0,1,0); l.BackgroundTransparency=1
    l.Text=t; l.TextColor3=Color3.fromRGB(200,200,200); l.Font=Enum.Font.Gotham
    l.TextSize=12; l.TextXAlignment=Enum.TextXAlignment.Left
    local b=Instance.new("TextButton",f); b.Size=UDim2.new(0,50,0,20)
    b.Position=UDim2.new(1,-50,.5,-10)
    b.BackgroundColor3=d and Color3.fromRGB(0,170,110) or Color3.fromRGB(70,70,85)
    b.Text=d and "ON" or "OFF"; b.TextColor3=Color3.new(1,1,1)
    b.Font=Enum.Font.GothamBold; b.TextSize=11
    Instance.new("UICorner",b).CornerRadius=UDim.new(0,4)
    local s=d
    b.MouseButton1Click:Connect(function()
        s=not s; b.Text=s and "ON" or "OFF"
        b.BackgroundColor3=s and Color3.fromRGB(0,170,110) or Color3.fromRGB(70,70,85)
        if cbk then cbk(s) end
    end)
end

local function btn(p,t,cb)
    local b=Instance.new("TextButton",p); b.Size=UDim2.new(1,0,0,26)
    b.BackgroundColor3=Color3.fromRGB(160,30,60); b.Text=t
    b.TextColor3=Color3.new(1,1,1); b.Font=Enum.Font.GothamBold; b.TextSize=12
    Instance.new("UICorner",b).CornerRadius=UDim.new(0,4)
    b.MouseButton1Click:Connect(cb)
end

cb.MouseButton1Click:Connect(function() g.Enabled=false end)
UIS.InputBegan:Connect(function(i,p)
    if not p and i.KeyCode==Enum.KeyCode.RightShift then g.Enabled=not g.Enabled end
end)

-- Globals
getgenv().AA={farm=false,upg=false,sum=false}

-- Helpers
local function remote(n)
    for _,d in ipairs({RS:FindFirstChild("Remotes"),RS:FindFirstChild("Events"),RS}) do
        if d then
            local o=d:FindFirstChild(n) or (d:FindFirstChild("Events") and d.Events:FindFirstChild(n))
            if o then return o end
        end
    end
end

local function tp(cf)
    local h=P.Character and P.Character:FindFirstChild("HumanoidRootPart")
    if h then h.CFrame=cf; h.AssemblyLinearVelocity=Vector3.zero; return true end
end

local function nearEnemy()
    local h=P.Character and P.Character:FindFirstChild("HumanoidRootPart")
    if not h then return end
    local best,md=nil,math.huge
    for _,o in ipairs(workspace:GetDescendants()) do
        if o:IsA("Model") and o:FindFirstChild("HumanoidRootPart") and
           (o:FindFirstChild("Humanoid") or o.Name:lower():find("enemy")) then
            local d=(o.HumanoidRootPart.Position-h.Position).Magnitude
            if d<md then md=d; best=o end
        end
    end
    return best
end

-- Farm tab
local s1=sec("Auto Farm")
tog(s1,"Auto Farm Enemy",false,function(v)
    getgenv().AA.farm=v
    if v then task.spawn(function()
        while getgenv().AA.farm do
            local e=nearEnemy()
            if e then
                tp(e.HumanoidRootPart.CFrame*CFrame.new(0,0,-3))
                task.wait(.15)
                local r=remote("SwordHit")
                if r then pcall(function() r:FireServer(e) end) end
            end
            task.wait(.3)
        end
    end) end
end)
tog(s1,"Auto Upgrade",false,function(v)
    getgenv().AA.upg=v
    if v then task.spawn(function()
        while getgenv().AA.upg do
            local r=remote("Upgrade"); if r then pcall(function() r:FireServer() end) end
            task.wait(1)
        end
    end) end
end)
tog(s1,"Auto Summon",false,function(v)
    getgenv().AA.sum=v
    if v then task.spawn(function()
        while getgenv().AA.sum do
            local r=remote("Summon"); if r then pcall(function() r:FireServer() end) end
            task.wait(2)
        end
    end) end
end)

-- Utility tab
local s2=sec("Utility")
btn(s2,"TP về Spawn",function() if tp(CFrame.new(0,10,0)) then print("TP spawn") end end)
btn(s2,"TP đến enemy gần nhất",function()
    local e=nearEnemy()
    if e then tp(e.HumanoidRootPart.CFrame*CFrame.new(0,5,-5)) end
end)
tog(s2,"Anti-AFK",true,function(v)
    if v then task.spawn(function()
        while v do
            game:GetService("VirtualUser"):ClickButton2(Vector2.new())
            task.wait(60)
        end
    end) end
end)

print("[AA Hub] Loaded. RightShift = toggle UI.")
