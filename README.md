local Players=game:GetService("Players")
local LocalPlayer=Players.LocalPlayer
local RunService=game:GetService("RunService")
local UIS=game:GetService("UserInputService")
local Workspace=game:GetService("Workspace")
local gui=Instance.new("ScreenGui")
gui.Parent=game.CoreGui
local xButton=Instance.new("TextButton")
xButton.Size=UDim2.new(0,30,0,30)
xButton.Position=UDim2.new(0.5,-15,0,50)
xButton.BackgroundColor3=Color3.new(0,0,0)
xButton.Text=""
xButton.Active=true
xButton.Draggable=true
xButton.Parent=gui
local frame=Instance.new("Frame")
frame.Size=UDim2.new(0,450,0,500)
frame.Position=UDim2.new(0.25,0,0.15,0)
frame.BackgroundColor3=Color3.fromRGB(30,30,30)
frame.Visible=false
frame.Active=true
frame.Draggable=true
frame.Parent=gui
xButton.MouseButton1Click:Connect(function() frame.Visible=not frame.Visible end)
local tabFrame=Instance.new("Frame")
tabFrame.Size=UDim2.new(0,120,1,0)
tabFrame.Position=UDim2.new(0,0,0,0)
tabFrame.BackgroundColor3=Color3.fromRGB(40,40,40)
tabFrame.Parent=frame
local tabs={"Skins","Teleport","Fliegen","Fun","Player","Admin","Tools","Misc","Trollen","Spiele","Weapons"}
local tabFrames={}
local flying=false
local flySpeed=50
local bodyVelocity,bodyGyro
local noclip=false
local function getPlayerList()
    local list={}
    for _,p in pairs(Players:GetPlayers()) do
        if p~=LocalPlayer then
            table.insert(list,p)
        end
    end
    return list
end
local function teleportTo(player)
    if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        LocalPlayer.Character.HumanoidRootPart.CFrame=player.Character.HumanoidRootPart.CFrame+Vector3.new(0,5,0)
    end
end
local function startFly()
    if flying then return end
    local hrp=LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if hrp then
        flying=true
        bodyVelocity=Instance.new("BodyVelocity")
        bodyVelocity.MaxForce=Vector3.new(400000,400000,400000)
        bodyVelocity.Velocity=Vector3.new(0,0,0)
        bodyVelocity.Parent=hrp
        bodyGyro=Instance.new("BodyGyro")
        bodyGyro.MaxTorque=Vector3.new(400000,400000,400000)
        bodyGyro.CFrame=hrp.CFrame
        bodyGyro.Parent=hrp
        RunService.RenderStepped:Connect(function()
            if not flying then return end
            local cam=Workspace.CurrentCamera
            local dir=Vector3.new(0,0,0)
            if UIS:IsKeyDown(Enum.KeyCode.W) then dir=dir+cam.CFrame.LookVector end
            if UIS:IsKeyDown(Enum.KeyCode.S) then dir=dir-cam.CFrame.LookVector end
            if UIS:IsKeyDown(Enum.KeyCode.A) then dir=dir-cam.CFrame.RightVector end
            if UIS:IsKeyDown(Enum.KeyCode.D) then dir=dir+cam.CFrame.RightVector end
            bodyVelocity.Velocity=dir*flySpeed
            bodyGyro.CFrame=cam.CFrame
        end)
    end
end
local function stopFly()
    flying=false
    if bodyVelocity then bodyVelocity:Destroy() end
    if bodyGyro then bodyGyro:Destroy() end
end
RunService.Stepped:Connect(function()
    if noclip and LocalPlayer.Character then
        for _,part in pairs(LocalPlayer.Character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide=false
            end
        end
    end
end)
local function setSize(scale)
    local char=LocalPlayer.Character
    if char and char:FindFirstChild("Humanoid") then
        char.Humanoid.BodyHeightScale.Value=scale
        char.Humanoid.BodyWidthScale.Value=scale
        char.Humanoid.BodyDepthScale.Value=scale
        char.Humanoid.HeadScale.Value=scale
    end
end
local function copySkin(player)
    if player and player.Character then
        local char=LocalPlayer.Character
        for _,part in pairs(player.Character:GetChildren()) do
            if part:IsA("Accessory") or part:IsA("Hat") then
                part:Clone().Parent=char
            end
        end
    end
end
for i,tabName in ipairs(tabs) do
    local btn=Instance.new("TextButton")
    btn.Size=UDim2.new(1,-10,0,35)
    btn.Position=UDim2.new(0,5,0,(i-1)*40+5)
    btn.BackgroundColor3=Color3.fromRGB(60,60,60)
    btn.TextColor3=Color3.new(1,1,1)
    btn.Text=tabName
    btn.Parent=tabFrame
    local contentFrame=Instance.new("Frame")
    contentFrame.Size=UDim2.new(0,310,1,0)
    contentFrame.Position=UDim2.new(0.28,0,0,0)
    contentFrame.BackgroundColor3=Color3.fromRGB(50,50,50)
    contentFrame.Visible=false
    contentFrame.Parent=frame
    local scroll=Instance.new("ScrollingFrame")
    scroll.Size=UDim2.new(1,-10,1,-10)
    scroll.Position=UDim2.new(0,5,0,5)
    scroll.CanvasSize=UDim2.new(0,0,0,500)
    scroll.ScrollBarThickness=8
    scroll.BackgroundTransparency=1
    scroll.Parent=contentFrame
    tabFrames[tabName]=contentFrame
    btn.MouseButton1Click:Connect(function()
        for _,f in pairs(tabFrames) do f.Visible=false end
        contentFrame.Visible=true
    end)
end
-- Skins Tab Buttons
if tabFrames["Skins"] then
    local features={"Copy Skin","Mini Size","Big Size","Random Skin","Change Hat","Change Shirt","Change Pants","Change Accessory","Reset Skin","Apply Player Skin","Full Body Clone"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Skins"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Copy Skin" then
                local list=getPlayerList()
                if #list>0 then copySkin(list[1]) end
            elseif feat=="Mini Size" then
                setSize(0.5)
            elseif feat=="Big Size" then
                setSize(3)
            elseif feat=="Random Skin" then
                local list=getPlayerList()
                if #list>0 then copySkin(list[math.random(1,#list)]) end
            elseif feat=="Change Hat" then
                local list=getPlayerList()
                if #list>0 and list[1].Character then
                    for _,part in pairs(list[1].Character:GetChildren()) do
                        if part:IsA("Hat") then part:Clone().Parent=LocalPlayer.Character end
                    end
                end
            elseif feat=="Change Shirt" then
                local shirt=LocalPlayer.Character:FindFirstChildOfClass("Shirt")
                if shirt then shirt.ShirtTemplate="rbxassetid://12345678" end
            elseif feat=="Change Pants" then
                local pants=LocalPlayer.Character:FindFirstChildOfClass("Pants")
                if pants then pants.PantsTemplate="rbxassetid://12345678" end
            elseif feat=="Change Accessory" then
                local list=getPlayerList()
                if #list>0 then
                    for _,part in pairs(list[1].Character:GetChildren()) do
                        if part:IsA("Accessory") then part:Clone().Parent=LocalPlayer.Character end
                    end
                end
            elseif feat=="Reset Skin" then
                LocalPlayer.Character:BreakJoints()
            elseif feat=="Apply Player Skin" then
                local list=getPlayerList()
                if #list>0 then copySkin(list[1]) end
            elseif feat=="Full Body Clone" then
                local list=getPlayerList()
                if #list>0 then
                    for _,part in pairs(list[1].Character:GetChildren()) do
                        if part:IsA("BasePart") or part:IsA("Accessory") then
                            part:Clone().Parent=LocalPlayer.Character
                        end
                    end
                end
            end
        end)
    end
end
-- Teleport Tab Buttons
if tabFrames["Teleport"] then
    local features={"Teleport to Player","Random Teleport","Spawn","House","School","Police","Hospital","Park","Shop","Bank","Parking"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Teleport"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Teleport to Player" then
                local list=getPlayerList()
                if #list>0 then teleportTo(list[1]) end
            elseif feat=="Random Teleport" then
                local list=getPlayerList()
                if #list>0 then teleportTo(list[math.random(1,#list)]) end
            elseif feat=="Spawn" and Workspace:FindFirstChild("SpawnLocation") then
                LocalPlayer.Character.HumanoidRootPart.CFrame=Workspace.SpawnLocation.CFrame+Vector3.new(0,5,0)
            elseif feat=="House" and Workspace:FindFirstChild("House") then
                LocalPlayer.Character.HumanoidRootPart.CFrame=Workspace.House.CFrame+Vector3.new(0,5,0)
            elseif feat=="School" and Workspace:FindFirstChild("School") then
                LocalPlayer.Character.HumanoidRootPart.CFrame=Workspace.School.CFrame+Vector3.new(0,5,0)
            elseif feat=="Police" and Workspace:FindFirstChild("Police") then
                LocalPlayer.Character.HumanoidRootPart.CFrame=Workspace.Police.CFrame+Vector3.new(0,5,0)
            elseif feat=="Hospital" and Workspace:FindFirstChild("Hospital") then
                LocalPlayer.Character.HumanoidRootPart.CFrame=Workspace.Hospital.CFrame+Vector3.new(0,5,0)
            elseif feat=="Park" and Workspace:FindFirstChild("Park") then
                LocalPlayer.Character.HumanoidRootPart.CFrame=Workspace.Park.CFrame+Vector3.new(0,5,0)
            elseif feat=="Shop" and Workspace:FindFirstChild("Shop") then
                LocalPlayer.Character.HumanoidRootPart.CFrame=Workspace.Shop.CFrame+Vector3.new(0,5,0)
            elseif feat=="Bank" and Workspace:FindFirstChild("Bank") then
                LocalPlayer.Character.HumanoidRootPart.CFrame=Workspace.Bank.CFrame+Vector3.new(0,5,0)
            elseif feat=="Parking" and Workspace:FindFirstChild("Parking") then
                LocalPlayer.Character.HumanoidRootPart.CFrame=Workspace.Parking.CFrame+Vector3.new(0,5,0)
            end
        end)
    end
endif tabFrames["Fliegen"] then
    local features={"Start Fly","Stop Fly","Increase Speed","Decrease Speed","Toggle Noclip","Enable Gravity","Disable Gravity","Hover","Fly Up","Fly Down","Reset Fly"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Fliegen"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Start Fly" then startFly()
            elseif feat=="Stop Fly" then stopFly()
            elseif feat=="Increase Speed" then flySpeed=flySpeed+10
            elseif feat=="Decrease Speed" then flySpeed=math.max(10,flySpeed-10)
            elseif feat=="Toggle Noclip" then noclip=not noclip
            elseif feat=="Enable Gravity" then LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity=Vector3.new(0,0,0); LocalPlayer.Character.Humanoid.UseJumpPower=true
            elseif feat=="Disable Gravity" then LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity=Vector3.new(0,0,0); LocalPlayer.Character.Humanoid.UseJumpPower=false
            elseif feat=="Hover" then if bodyVelocity then bodyVelocity.Velocity=Vector3.new(0,0,0) end
            elseif feat=="Fly Up" then if bodyVelocity then bodyVelocity.Velocity=bodyVelocity.Velocity+Vector3.new(0,flySpeed,0) end
            elseif feat=="Fly Down" then if bodyVelocity then bodyVelocity.Velocity=bodyVelocity.Velocity-Vector3.new(0,flySpeed,0) end
            elseif feat=="Reset Fly" then stopFly()
            end
        end)
    end
end

-- Fun Tab Buttons
if tabFrames["Fun"] then
    local features={"Super Jump","Sit","Dance","Wave","Laugh","Spin","Grow","Shrink","Explode","Speed Boost","Reset Fun"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Fun"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Super Jump" then LocalPlayer.Character.Humanoid.JumpPower=200; LocalPlayer.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            elseif feat=="Sit" then LocalPlayer.Character.Humanoid.Sit=true
            elseif feat=="Dance" then LocalPlayer.Character.Humanoid:LoadAnimation(Instance.new("Animation",LocalPlayer.Character)).AnimationId="rbxassetid://123456"
            elseif feat=="Wave" then LocalPlayer.Character.Humanoid:LoadAnimation(Instance.new("Animation",LocalPlayer.Character)).AnimationId="rbxassetid://123457"
            elseif feat=="Laugh" then LocalPlayer.Character.Humanoid:LoadAnimation(Instance.new("Animation",LocalPlayer.Character)).AnimationId="rbxassetid://123458"
            elseif feat=="Spin" then LocalPlayer.Character.HumanoidRootPart.CFrame=LocalPlayer.Character.HumanoidRootPart.CFrame*CFrame.Angles(0,math.rad(360),0)
            elseif feat=="Grow" then setSize(2)
            elseif feat=="Shrink" then setSize(0.5)
            elseif feat=="Explode" then LocalPlayer.Character.HumanoidRootPart:BreakJoints()
            elseif feat=="Speed Boost" then LocalPlayer.Character.Humanoid.WalkSpeed=50
            elseif feat=="Reset Fun" then LocalPlayer.Character.Humanoid.WalkSpeed=16; setSize(1)
            end
        end)
    end
endif tabFrames["Player"] then
    local features={"View Player","Kick Player","Ban Player","Teleport to Player","Follow Player","Copy Skin","Change Name","Freeze Player","Unfreeze Player","Give Tool","Reset Player"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Player"]
        fbtn.MouseButton1Click:Connect(function()
            local list=getPlayerList()
            local target=list[1]
            if feat=="View Player" and target then
                Workspace.CurrentCamera.CameraSubject=target.Character.Humanoid
            elseif feat=="Kick Player" and target then
                if target~=LocalPlayer then target:Kick("Kicked by hub") end
            elseif feat=="Ban Player" and target then
                if target~=LocalPlayer then target:Kick("Banned by hub") end
            elseif feat=="Teleport to Player" and target then
                teleportTo(target)
            elseif feat=="Follow Player" and target then
                RunService.RenderStepped:Connect(function()
                    if target.Character and LocalPlayer.Character then
                        LocalPlayer.Character.HumanoidRootPart.CFrame=target.Character.HumanoidRootPart.CFrame+Vector3.new(0,5,0)
                    end
                end)
            elseif feat=="Copy Skin" and target then
                copySkin(target)
            elseif feat=="Change Name" and target then
                target.Name="Player_"..math.random(1,1000)
            elseif feat=="Freeze Player" and target and target.Character then
                for _,part in pairs(target.Character:GetDescendants()) do
                    if part:IsA("BasePart") then part.Anchored=true end
                end
            elseif feat=="Unfreeze Player" and target and target.Character then
                for _,part in pairs(target.Character:GetDescendants()) do
                    if part:IsA("BasePart") then part.Anchored=false end
                end
            elseif feat=="Give Tool" and target then
                local tool=Instance.new("Tool")
                tool.Name="HubTool"
                tool.Parent=target.Backpack
            elseif feat=="Reset Player" and target and target.Character then
                target.Character:BreakJoints()
            end
        end)
    end
end

if tabFrames["Admin"] then
    local features={"Kick All","Teleport All to Me","Give Admin Tool","Reset All","Freeze All","Unfreeze All","Server Shutdown","Change Weather","Give Money","Remove Tools","Ban All"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Admin"]
        fbtn.MouseButton1Click:Connect(function()
            local list=getPlayerList()
            if feat=="Kick All" then
                for _,p in pairs(list) do if p~=LocalPlayer then p:Kick("Kicked by hub") end end
            elseif feat=="Teleport All to Me" then
                for _,p in pairs(list) do teleportTo(LocalPlayer) end
            elseif feat=="Give Admin Tool" then
                for _,p in pairs(list) do
                    local tool=Instance.new("Tool")
                    tool.Name="AdminTool"
                    tool.Parent=p.Backpack
                end
            elseif feat=="Reset All" then
                for _,p in pairs(list) do if p.Character then p.Character:BreakJoints() end end
            elseif feat=="Freeze All" then
                for _,p in pairs(list) do if p.Character then for _,part in pairs(p.Character:GetDescendants()) do if part:IsA("BasePart") then part.Anchored=true end end end end
            elseif feat=="Unfreeze All" then
                for _,p in pairs(list) do if p.Character then for _,part in pairs(p.Character:GetDescendants()) do if part:IsA("BasePart") then part.Anchored=false end end end end
            elseif feat=="Server Shutdown" then
                for _,p in pairs(list) do if p~=LocalPlayer then p:Kick("Server shutting down") end end
            elseif feat=="Change Weather" then
                if Workspace:FindFirstChild("Lighting") then Workspace.Lighting.FogEnd=1000 end
            elseif feat=="Give Money" then
                -- Placeholder: integrate with game currency
            elseif feat=="Remove Tools" then
                for _,p in pairs(list) do for _,tool in pairs(p.Backpack:GetChildren()) do tool:Destroy() end end
            elseif feat=="Ban All" then
                for _,p in pairs(list) do if p~=LocalPlayer then p:Kick("Banned by hub") end end
            end
        end)
    end
endif tabFrames["Tools"] then
    local features={"Spawn Tool","Remove Tool","Duplicate Tool","Change Tool Color","Equip Tool","Unequip Tool","Give Tool to Player","Remove Tool from Player","Tool Speed Boost","Reset Tool","Random Tool"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Tools"]
        fbtn.MouseButton1Click:Connect(function()
            local char=LocalPlayer.Character
            local backpack=LocalPlayer.Backpack
            if feat=="Spawn Tool" then
                local tool=Instance.new("Tool")
                tool.Name="HubTool"
                tool.Parent=backpack
            elseif feat=="Remove Tool" then
                for _,tool in pairs(backpack:GetChildren()) do tool:Destroy() end
            elseif feat=="Duplicate Tool" then
                for _,tool in pairs(backpack:GetChildren()) do
                    local clone=tool:Clone()
                    clone.Parent=backpack
                end
            elseif feat=="Change Tool Color" then
                for _,tool in pairs(backpack:GetChildren()) do
                    if tool:IsA("Tool") and tool:FindFirstChild("Handle") then
                        tool.Handle.BrickColor=BrickColor.Random()
                    end
                end
            elseif feat=="Equip Tool" then
                for _,tool in pairs(backpack:GetChildren()) do
                    if tool:IsA("Tool") then tool.Parent=char end
                end
            elseif feat=="Unequip Tool" then
                for _,tool in pairs(char:GetChildren()) do
                    if tool:IsA("Tool") then tool.Parent=backpack end
                end
            elseif feat=="Give Tool to Player" then
                local list=getPlayerList()
                if #list>0 then
                    local tool=Instance.new("Tool")
                    tool.Name="HubTool"
                    tool.Parent=list[1].Backpack
                end
            elseif feat=="Remove Tool from Player" then
                local list=getPlayerList()
                if #list>0 then
                    for _,tool in pairs(list[1].Backpack:GetChildren()) do tool:Destroy() end
                end
            elseif feat=="Tool Speed Boost" then
                char.Humanoid.WalkSpeed=50
            elseif feat=="Reset Tool" then
                char.Humanoid.WalkSpeed=16
            elseif feat=="Random Tool" then
                local tool=Instance.new("Tool")
                tool.Name="RandomTool_"..math.random(1,1000)
                tool.Parent=backpack
            end
        end)
    end
end

if tabFrames["Misc"] then
    local features={"Enable Noclip","Disable Noclip","Toggle Fly","Reset Fly","Super Speed","Reset Speed","Infinite Jump","Reset Jump","Enable Gravity","Disable Gravity","Reset All"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Misc"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Enable Noclip" then noclip=true
            elseif feat=="Disable Noclip" then noclip=false
            elseif feat=="Toggle Fly" then if flying then stopFly() else startFly() end
            elseif feat=="Reset Fly" then stopFly()
            elseif feat=="Super Speed" then LocalPlayer.Character.Humanoid.WalkSpeed=100
            elseif feat=="Reset Speed" then LocalPlayer.Character.Humanoid.WalkSpeed=16
            elseif feat=="Infinite Jump" then
                LocalPlayer.Character.Humanoid.JumpPower=200
                UIS.InputBegan:Connect(function(input,gpe)
                    if input.UserInputType==Enum.UserInputType.Keyboard and input.KeyCode==Enum.KeyCode.Space then
                        LocalPlayer.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
                    end
                end)
            elseif feat=="Reset Jump" then LocalPlayer.Character.Humanoid.JumpPower=50
            elseif feat=="Enable Gravity" then LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity=Vector3.new(0,0,0); LocalPlayer.Character.Humanoid.UseJumpPower=true
            elseif feat=="Disable Gravity" then LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity=Vector3.new(0,0,0); LocalPlayer.Character.Humanoid.UseJumpPower=false
            elseif feat=="Reset All" then stopFly(); noclip=false; LocalPlayer.Character.Humanoid.WalkSpeed=16; LocalPlayer.Character.Humanoid.JumpPower=50
            end
        end)
    end
endif tabFrames["Teleport"] then
    local features={"Teleport to Player","Teleport to Random","Teleport to Spawn","Teleport to Admin","Teleport Up","Teleport Down","Teleport Forward","Teleport Backward","Teleport to Tool","Teleport to Item","Reset Teleport"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Teleport"]
        fbtn.MouseButton1Click:Connect(function()
            local list=getPlayerList()
            local target=list[1]
            if feat=="Teleport to Player" and target then teleportTo(target)
            elseif feat=="Teleport to Random" and #list>0 then teleportTo(list[math.random(1,#list)])
            elseif feat=="Teleport to Spawn" then LocalPlayer.Character.HumanoidRootPart.CFrame=CFrame.new(Vector3.new(0,5,0))
            elseif feat=="Teleport to Admin" and target then teleportTo(LocalPlayer)
            elseif feat=="Teleport Up" then LocalPlayer.Character.HumanoidRootPart.CFrame=LocalPlayer.Character.HumanoidRootPart.CFrame+CFrame.new(0,50,0)
            elseif feat=="Teleport Down" then LocalPlayer.Character.HumanoidRootPart.CFrame=LocalPlayer.Character.HumanoidRootPart.CFrame+CFrame.new(0,-50,0)
            elseif feat=="Teleport Forward" then
                local hrp=LocalPlayer.Character.HumanoidRootPart
                hrp.CFrame=hrp.CFrame*CFrame.new(0,0,-50)
            elseif feat=="Teleport Backward" then
                local hrp=LocalPlayer.Character.HumanoidRootPart
                hrp.CFrame=hrp.CFrame*CFrame.new(0,0,50)
            elseif feat=="Teleport to Tool" then
                local tool=workspace:FindFirstChildWhichIsA("Tool")
                if tool then LocalPlayer.Character.HumanoidRootPart.CFrame=tool.Handle.CFrame end
            elseif feat=="Teleport to Item" then
                local item=workspace:FindFirstChild("Item")
                if item then LocalPlayer.Character.HumanoidRootPart.CFrame=item.CFrame end
            elseif feat=="Reset Teleport" then LocalPlayer.Character.HumanoidRootPart.CFrame=CFrame.new(Vector3.new(0,5,0))
            end
        end)
    end
end

if tabFrames["Skins"] then
    local features={"Copy Skin","Change Skin Color","Random Skin","Mini Size","Max Size","Reset Size","Set Custom Size","Equip Hat","Remove Hat","Equip Shirt","Equip Pants"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Skins"]
        fbtn.MouseButton1Click:Connect(function()
            local char=LocalPlayer.Character
            if feat=="Copy Skin" then
                copySkin(LocalPlayer)
            elseif feat=="Change Skin Color" then
                for _,part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then part.BrickColor=BrickColor.Random() end
                end
            elseif feat=="Random Skin" then
                for _,part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then part.BrickColor=BrickColor.Random() end
                end
            elseif feat=="Mini Size" then setSize(0.5)
            elseif feat=="Max Size" then setSize(1000)
            elseif feat=="Reset Size" then setSize(1)
            elseif feat=="Set Custom Size" then
                local input=tonumber(promptUser("Enter size 0.1-1000"))
                if input then setSize(input) end
            elseif feat=="Equip Hat" then
                local hat=Instance.new("Accessory")
                hat.Name="HubHat"
                hat.Parent=char
            elseif feat=="Remove Hat" then
                for _,acc in pairs(char:GetChildren()) do if acc:IsA("Accessory") then acc:Destroy() end end
            elseif feat=="Equip Shirt" then
                local shirt=Instance.new("Shirt")
                shirt.ShirtTemplate="rbxassetid://123456"
                shirt.Parent=char
            elseif feat=="Equip Pants" then
                local pants=Instance.new("Pants")
                pants.PantsTemplate="rbxassetid://123457"
                pants.Parent=char
            end
        end)
    end
endif tabFrames["Troll"] then
    local features={"Spin Player","Freeze Player","Launch Player","Change WalkSpeed","Change JumpPower","Invisible Player","Clone Player","Shrink Player","Grow Player","Explode Player","Reset Player"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Troll"]
        fbtn.MouseButton1Click:Connect(function()
            local list=getPlayerList()
            local target=list[1]
            if not target then return end
            local char=target.Character
            if feat=="Spin Player" and char then
                RunService.RenderStepped:Connect(function()
                    char.HumanoidRootPart.CFrame=char.HumanoidRootPart.CFrame*CFrame.Angles(0,math.rad(30),0)
                end)
            elseif feat=="Freeze Player" and char then
                for _,part in pairs(char:GetDescendants()) do if part:IsA("BasePart") then part.Anchored=true end end
            elseif feat=="Launch Player" and char then
                char.HumanoidRootPart.Velocity=Vector3.new(0,100,0)
            elseif feat=="Change WalkSpeed" and char then
                char.Humanoid.WalkSpeed=50
            elseif feat=="Change JumpPower" and char then
                char.Humanoid.JumpPower=200
            elseif feat=="Invisible Player" and char then
                for _,part in pairs(char:GetDescendants()) do if part:IsA("BasePart") then part.Transparency=1 end end
            elseif feat=="Clone Player" and char then
                local clone=char:Clone()
                clone.Parent=workspace
                clone:SetPrimaryPartCFrame(char.HumanoidRootPart.CFrame + Vector3.new(5,0,0))
            elseif feat=="Shrink Player" and char then
                setSizeChar(char,0.5)
            elseif feat=="Grow Player" and char then
                setSizeChar(char,5)
            elseif feat=="Explode Player" and char then
                char:BreakJoints()
            elseif feat=="Reset Player" and char then
                setSizeChar(char,1)
                for _,part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.Anchored=false
                        part.Transparency=0
                    end
                end
                char.Humanoid.WalkSpeed=16
                char.Humanoid.JumpPower=50
            end
        end)
    end
end

if tabFrames["Games"] then
    local features={"Start Minigame","End Minigame","Give Points","Reset Points","Teleport to Game","Random Game Event","Change Game Map","Spawn Game Items","Remove Game Items","Enable Game Boost","Reset Game"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Games"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Start Minigame" then startMinigame()
            elseif feat=="End Minigame" then endMinigame()
            elseif feat=="Give Points" then givePoints(100)
            elseif feat=="Reset Points" then resetPoints()
            elseif feat=="Teleport to Game" then LocalPlayer.Character.HumanoidRootPart.CFrame=CFrame.new(Vector3.new(0,5,0))
            elseif feat=="Random Game Event" then randomEvent()
            elseif feat=="Change Game Map" then changeMap()
            elseif feat=="Spawn Game Items" then spawnItems()
            elseif feat=="Remove Game Items" then removeItems()
            elseif feat=="Enable Game Boost" then enableBoost()
            elseif feat=="Reset Game" then resetGame()
            end
        end)
    end
endif tabFrames["Fly"] then
    local features={"Start Fly","Stop Fly","Fly Up","Fly Down","Fly Forward","Fly Backward","Increase Speed","Decrease Speed","Set Max Height","Set Min Height","Reset Fly"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Fly"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Start Fly" then startFly()
            elseif feat=="Stop Fly" then stopFly()
            elseif feat=="Fly Up" then moveFly(Vector3.new(0,50,0))
            elseif feat=="Fly Down" then moveFly(Vector3.new(0,-50,0))
            elseif feat=="Fly Forward" then
                local hrp=LocalPlayer.Character.HumanoidRootPart
                hrp.CFrame=hrp.CFrame*CFrame.new(0,0,-50)
            elseif feat=="Fly Backward" then
                local hrp=LocalPlayer.Character.HumanoidRootPart
                hrp.CFrame=hrp.CFrame*CFrame.new(0,0,50)
            elseif feat=="Increase Speed" then flySpeed=flySpeed+10
            elseif feat=="Decrease Speed" then flySpeed=math.max(10,flySpeed-10)
            elseif feat=="Set Max Height" then maxFlyHeight=tonumber(promptUser("Enter max height")) or 500
            elseif feat=="Set Min Height" then minFlyHeight=tonumber(promptUser("Enter min height")) or 0
            elseif feat=="Reset Fly" then
                flySpeed=50
                maxFlyHeight=500
                minFlyHeight=0
                stopFly()
            end
        end)
    end
end

if tabFrames["Fun"] then
    local features={"Dance","Sit","Wave","Laugh","Cry","Jump","Spin","Emote1","Emote2","Emote3","Reset Fun"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Fun"]
        fbtn.MouseButton1Click:Connect(function()
            local char=LocalPlayer.Character
            if not char then return end
            if feat=="Dance" then playAnim("Dance")
            elseif feat=="Sit" then playAnim("Sit")
            elseif feat=="Wave" then playAnim("Wave")
            elseif feat=="Laugh" then playAnim("Laugh")
            elseif feat=="Cry" then playAnim("Cry")
            elseif feat=="Jump" then char.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            elseif feat=="Spin" then
                RunService.RenderStepped:Connect(function()
                    char.HumanoidRootPart.CFrame=char.HumanoidRootPart.CFrame*CFrame.Angles(0,math.rad(30),0)
                end)
            elseif feat=="Emote1" then playAnim("Emote1")
            elseif feat=="Emote2" then playAnim("Emote2")
            elseif feat=="Emote3" then playAnim("Emote3")
            elseif feat=="Reset Fun" then resetEmotes()
            end
        end)
    end
endif tabFrames["Admin"] then
    local features={"Kick Player","Ban Player","Mute Player","Unmute Player","Reset Player","Give Admin","Remove Admin","Change Rank","Teleport Admin","Spawn Admin Item","Reset Admin Actions"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Admin"]
        fbtn.MouseButton1Click:Connect(function()
            local list=getPlayerList()
            local target=list[1]
            if not target then return end
            if feat=="Kick Player" then kickPlayer(target)
            elseif feat=="Ban Player" then banPlayer(target)
            elseif feat=="Mute Player" then mutePlayer(target)
            elseif feat=="Unmute Player" then unmutePlayer(target)
            elseif feat=="Reset Player" then resetChar(target)
            elseif feat=="Give Admin" then giveAdmin(target)
            elseif feat=="Remove Admin" then removeAdmin(target)
            elseif feat=="Change Rank" then changeRank(target,promptUser("Enter rank"))
            elseif feat=="Teleport Admin" then teleportTo(target)
            elseif feat=="Spawn Admin Item" then spawnAdminItem()
            elseif feat=="Reset Admin Actions" then resetAdminActions()
            end
        end)
    end
end

if tabFrames["Player"] then
    local features={"Reset Character","Change WalkSpeed","Change JumpPower","Toggle Sit","Toggle Crouch","Equip Tool","Unequip Tool","Change Skin","Randomize Appearance","Toggle Noclip","Reset All"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Player"]
        fbtn.MouseButton1Click:Connect(function()
            local char=LocalPlayer.Character
            if not char then return end
            if feat=="Reset Character" then resetChar(LocalPlayer)
            elseif feat=="Change WalkSpeed" then char.Humanoid.WalkSpeed=tonumber(promptUser("Enter WalkSpeed")) or 16
            elseif feat=="Change JumpPower" then char.Humanoid.JumpPower=tonumber(promptUser("Enter JumpPower")) or 50
            elseif feat=="Toggle Sit" then char.Humanoid.Sit=true
            elseif feat=="Toggle Crouch" then toggleCrouch(char)
            elseif feat=="Equip Tool" then equipTool("HubTool")
            elseif feat=="Unequip Tool" then unequipTools()
            elseif feat=="Change Skin" then changeSkin(LocalPlayer)
            elseif feat=="Randomize Appearance" then randomizeAppearance(LocalPlayer)
            elseif feat=="Toggle Noclip" then noclip=not noclip
            elseif feat=="Reset All" then
                resetChar(LocalPlayer)
                char.Humanoid.WalkSpeed=16
                char.Humanoid.JumpPower=50
                noclip=false
            end
        end)
    end
endif tabFrames["Misc"] then
    local features={"Toggle GUI","Enable Night Mode","Disable Night Mode","Show FPS","Hide FPS","Play Sound","Stop Sound","Enable Music","Disable Music","Reset Settings","Random Event"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Misc"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Toggle GUI" then mainGui.Enabled=not mainGui.Enabled
            elseif feat=="Enable Night Mode" then setLighting("Night")
            elseif feat=="Disable Night Mode" then setLighting("Day")
            elseif feat=="Show FPS" then showFPS(true)
            elseif feat=="Hide FPS" then showFPS(false)
            elseif feat=="Play Sound" then playSound("HubSound")
            elseif feat=="Stop Sound" then stopSound("HubSound")
            elseif feat=="Enable Music" then enableMusic(true)
            elseif feat=="Disable Music" then enableMusic(false)
            elseif feat=="Reset Settings" then resetAllSettings()
            elseif feat=="Random Event" then triggerRandomEvent()
            end
        end)
    end
end

if tabFrames["Settings"] then
    local features={"Change Keybinds","Reset Keybinds","Change UI Color","Reset UI Color","Toggle Notifications","Reset Notifications","Change Volume","Reset Volume","Enable Auto-Update","Disable Auto-Update","Reset All Settings"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Settings"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Change Keybinds" then changeKeybinds()
            elseif feat=="Reset Keybinds" then resetKeybinds()
            elseif feat=="Change UI Color" then changeUIColor(promptUser("Enter Color Hex"))
            elseif feat=="Reset UI Color" then resetUIColor()
            elseif feat=="Toggle Notifications" then toggleNotifications()
            elseif feat=="Reset Notifications" then resetNotifications()
            elseif feat=="Change Volume" then setVolume(tonumber(promptUser("Enter Volume 0-100")))
            elseif feat=="Reset Volume" then resetVolume()
            elseif feat=="Enable Auto-Update" then autoUpdate=true
            elseif feat=="Disable Auto-Update" then autoUpdate=false
            elseif feat=="Reset All Settings" then resetAllSettings()
            end
        end)
    end
endbinds","Reset Keybinds","Change UI Color","Reset UI Color","Toggle Notifications","Reset Notifications","Change Volume","Reset Volume","Enable Auto-Update","Disable Auto-Update","Reset All Settings"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Settings"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Change Keybinds" then changeKeybinds()
            elseif feat=="Reset Keybinds" then resetKeybinds()
            elseif feat=="Change UI Color" then changeUIColor(promptUser("Enter Color Hex"))
            elseif feat=="Reset UI Color" then resetUIColor()
            elseif feat=="Toggle Notifications" then toggleNotifications()
            elseif feat=="Reset Notifications" then resetNotifications()
            elseif feat=="Change Volume" then setVolume(tonumber(promptUser("Enter Volume 0-100")))
            elseif feat=="Reset Volume" then resetVolume()
            elseif feat=="Enable Auto-Update" then autoUpdate=true
            elseif feat=="Disable Auto-Update" then autoUpdate=false
            elseif feat=="Reset All Settings" then resetAllSettings()
            end
        end)
    end
end

if tabFrames["Fun"] then
    local features={"Dance","Sit","Wave","Jump","Spin","Laugh","Cry","Clap","Facepalm","Random Emote","Reset Emotes"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Fun"]
        fbtn.MouseButton1Click:Connect(function()
            local char=LocalPlayer.Character
            if not char then return end
            if feat=="Dance" then playEmote("Dance")
            elseif feat=="Sit" then char.Humanoid.Sit=true
            elseif feat=="Wave" then playEmote("Wave")
            elseif feat=="Jump" then char.Humanoid.Jump=true
            elseif feat=="Spin" then spinCharacter(char)
            elseif feat=="Laugh" then playEmote("Laugh")
            elseif feat=="Cry" then playEmote("Cry")
            elseif feat=="Clap" then playEmote("Clap")
            elseif feat=="Facepalm" then playEmote("Facepalm")
            elseif feat=="Random Emote" then playRandomEmote()
            elseif feat=="Reset Emotes" then resetEmotes()
            end
        end)
    end
endif tabFrames["Fly"] then
    local features={"Enable Fly","Disable Fly","Increase Speed","Decrease Speed","Fly Up","Fly Down","Hover","Reset Fly","Fly To Player","Fly To Random","Fly To Spawn"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Fly"]
        fbtn.MouseButton1Click:Connect(function()
            local char=LocalPlayer.Character
            if not char then return end
            if feat=="Enable Fly" then enableFly(char)
            elseif feat=="Disable Fly" then disableFly(char)
            elseif feat=="Increase Speed" then changeFlySpeed(char,1)
            elseif feat=="Decrease Speed" then changeFlySpeed(char,-1)
            elseif feat=="Fly Up" then flyMove(char,Vector3.new(0,5,0))
            elseif feat=="Fly Down" then flyMove(char,Vector3.new(0,-5,0))
            elseif feat=="Hover" then toggleHover(char)
            elseif feat=="Reset Fly" then resetFly(char)
            elseif feat=="Fly To Player" then flyToPlayer(choosePlayer(getPlayerList()))
            elseif feat=="Fly To Random" then flyToPlayer(getPlayerList()[math.random(#getPlayerList())])
            elseif feat=="Fly To Spawn" then flyToCFrame(char,spawnCFrame)
            end
        end)
    end
end

if tabFrames["Skins"] then
    local features={"Copy Skin","Apply Random Skin","Reset Skin","Change Head","Change Body","Change Arms","Change Legs","Change Hair","Change Hat","Change Face","Reset All Skin"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Skins"]
        fbtn.MouseButton1Click:Connect(function()
            local target=choosePlayer(getPlayerList())
            if feat=="Copy Skin" and target then copySkin(target)
            elseif feat=="Apply Random Skin" then randomizeAppearance(LocalPlayer)
            elseif feat=="Reset Skin" then resetAppearance(LocalPlayer)
            elseif feat=="Change Head" then changeBodyPart(LocalPlayer,"Head")
            elseif feat=="Change Body" then changeBodyPart(LocalPlayer,"Torso")
            elseif feat=="Change Arms" then changeBodyPart(LocalPlayer,"Arms")
            elseif feat=="Change Legs" then changeBodyPart(LocalPlayer,"Legs")
            elseif feat=="Change Hair" then changeHair(LocalPlayer)
            elseif feat=="Change Hat" then changeHat(LocalPlayer)
            elseif feat=="Change Face" then changeFace(LocalPlayer)
            elseif feat=="Reset All Skin" then resetAppearance(LocalPlayer)
            end
        end)
    end
endif tabFrames["Misc"] then
    local features={
        "Rejoin Server","Server Hop","Copy JobId","FPS Boost","Anti AFK",
        "Toggle Music","Change FOV","Reset Camera","Hide UI","Show UI",
        "Toggle Shadows","Low Graphics","High Graphics","Toggle Notifications",
        "Clear Workspace","Respawn At Place","Random Teleport","Spin Character",
        "Stop Spin","Float","Stop Float","Reset Misc"
    }
    local spinning=false
    local floating=false
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Misc"]
        fbtn.MouseButton1Click:Connect(function()
            local char=LocalPlayer.Character
            if feat=="Rejoin Server" then
                TeleportService:Teleport(game.PlaceId,LocalPlayer)
            elseif feat=="Server Hop" then
                serverHop()
            elseif feat=="Copy JobId" then
                setclipboard(game.JobId)
            elseif feat=="FPS Boost" then
                setfpscap(999)
            elseif feat=="Anti AFK" then
                antiAfk()
            elseif feat=="Toggle Music" then
                toggleMusic()
            elseif feat=="Change FOV" then
                workspace.CurrentCamera.FieldOfView=tonumber(promptUser("FOV")) or 70
            elseif feat=="Reset Camera" then
                workspace.CurrentCamera.FieldOfView=70
            elseif feat=="Hide UI" then
                mainGui.Enabled=false
            elseif feat=="Show UI" then
                mainGui.Enabled=true
            elseif feat=="Toggle Shadows" then
                Lighting.GlobalShadows=not Lighting.GlobalShadows
            elseif feat=="Low Graphics" then
                setLowGraphics()
            elseif feat=="High Graphics" then
                setHighGraphics()
            elseif feat=="Toggle Notifications" then
                notifications=not notifications
            elseif feat=="Clear Workspace" then
                for _,v in pairs(workspace:GetChildren()) do
                    if v:IsA("Part") and not v.Anchored then v:Destroy() end
                end
            elseif feat=="Respawn At Place" then
                resetChar(LocalPlayer)
            elseif feat=="Random Teleport" and char then
                char:MoveTo(Vector3.new(math.random(-500,500),50,math.random(-500,500)))
            elseif feat=="Spin Character" and char then
                spinning=true
                task.spawn(function()
                    while spinning and char and char:FindFirstChild("HumanoidRootPart") do
                        char.HumanoidRootPart.CFrame*=CFrame.Angles(0,math.rad(10),0)
                        task.wait()
                    end
                end)
            elseif feat=="Stop Spin" then
                spinning=false
            elseif feat=="Float" and char then
                floating=true
                task.spawn(function()
                    while floating and char and char:FindFirstChild("HumanoidRootPart") do
                        char.HumanoidRootPart.Velocity=Vector3.new(0,50,0)
                        task.wait()
                    end
                end)
            elseif feat=="Stop Float" then
                floating=false
            elseif feat=="Reset Misc" then
                spinning=false
                floating=false
                workspace.CurrentCamera.FieldOfView=70
                Lighting.GlobalShadows=true
            end
        end)
    end
endif tabFrames["Settings"] then
    local features={
        "Toggle Drag","Change Theme","Reset Theme","UI Transparency",
        "UI Scale","Toggle Blur","Reset UI","Save Config","Load Config",
        "Reset Settings"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Settings"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Toggle Drag" then
                uiDraggable=not uiDraggable
                setDraggable(mainFrame,uiDraggable)
            elseif feat=="Change Theme" then
                applyTheme(promptUser("Theme name"))
            elseif feat=="Reset Theme" then
                resetTheme()
            elseif feat=="UI Transparency" then
                mainFrame.BackgroundTransparency=tonumber(promptUser("0-1")) or 0
            elseif feat=="UI Scale" then
                mainFrame.UIScale.Scale=tonumber(promptUser("Scale")) or 1
            elseif feat=="Toggle Blur" then
                toggleBlur()
            elseif feat=="Reset UI" then
                mainFrame.Position=UDim2.new(0.5,-250,0.5,-200)
                mainFrame.BackgroundTransparency=0
            elseif feat=="Save Config" then
                saveConfig()
            elseif feat=="Load Config" then
                loadConfig()
            elseif feat=="Reset Settings" then
                resetTheme()
                uiDraggable=true
            end
        end)
    end
end

if tabFrames["About"] then
    local info={
        "Script Name: Bronixt Hub",
        "Version: 1.0",
        "Creator: Bronixt",
        "UI: Custom",
        "Status: Loaded"
    }
    for i,text in ipairs(info) do
        local lbl=Instance.new("TextLabel")
        lbl.Size=UDim2.new(1,-10,0,30)
        lbl.Position=UDim2.new(0,5,0,(i-1)*35)
        lbl.BackgroundTransparency=1
        lbl.TextColor3=Color3.new(1,1,1)
        lbl.TextXAlignment=Enum.TextXAlignment.Left
        lbl.Text=text
        lbl.Parent=tabFrames["About"]
    end
endif tabFrames["Visual"] then
    local features={
        "ESP Players","ESP Boxes","ESP Names","ESP Distance","ESP Health",
        "Chams","Remove Chams","Tracers","Crosshair","FOV Circle","Reset Visuals"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Visual"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="ESP Players" then toggleESP()
            elseif feat=="ESP Boxes" then toggleESPBoxes()
            elseif feat=="ESP Names" then toggleESPNames()
            elseif feat=="ESP Distance" then toggleESPDistance()
            elseif feat=="ESP Health" then toggleESPHealth()
            elseif feat=="Chams" then enableChams()
            elseif feat=="Remove Chams" then disableChams()
            elseif feat=="Tracers" then toggleTracers()
            elseif feat=="Crosshair" then toggleCrosshair()
            elseif feat=="FOV Circle" then toggleFOVCircle()
            elseif feat=="Reset Visuals" then resetVisuals()
            end
        end)
    end
end

if tabFrames["Combat"] then
    local features={
        "Aimbot","Silent Aim","Trigger Bot","Auto Shoot","Hitbox Expander",
        "No Recoil","No Spread","Fast Reload","Kill Aura","Auto Farm","Reset Combat"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Combat"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Aimbot" then toggleAimbot()
            elseif feat=="Silent Aim" then toggleSilentAim()
            elseif feat=="Trigger Bot" then toggleTriggerBot()
            elseif feat=="Auto Shoot" then toggleAutoShoot()
            elseif feat=="Hitbox Expander" then toggleHitbox()
            elseif feat=="No Recoil" then toggleNoRecoil()
            elseif feat=="No Spread" then toggleNoSpread()
            elseif feat=="Fast Reload" then toggleFastReload()
            elseif feat=="Kill Aura" then toggleKillAura()
            elseif feat=="Auto Farm" then toggleAutoFarm()
            elseif feat=="Reset Combat" then resetCombat()
            end
        end)
    end
end

if tabFrames["Movement"] then
    local features={
        "Speed Hack","Jump Hack","Fly","Teleport Walk","Dash",
        "Wall Climb","Spider","Bunny Hop","Slide","Auto Sprint","Reset Movement"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Movement"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Speed Hack" then toggleSpeed()
            elseif feat=="Jump Hack" then toggleJump()
            elseif feat=="Fly" then toggleFly()
            elseif feat=="Teleport Walk" then toggleTPWalk()
            elseif feat=="Dash" then dash()
            elseif feat=="Wall Climb" then toggleWallClimb()
            elseif feat=="Spider" then toggleSpider()
            elseif feat=="Bunny Hop" then toggleBhop()
            elseif feat=="Slide" then slide()
            elseif feat=="Auto Sprint" then toggleSprint()
            elseif feat=="Reset Movement" then resetMovement()
            end
        end)
    end
endif tabFrames["Teleport"] then
    local features={
        "Teleport to Player","Teleport Random","Teleport Spawn","Teleport House",
        "Teleport Police","Teleport Hospital","Teleport School","Teleport Bank",
        "Teleport Map Center","Save Position","Load Position"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Teleport"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Teleport to Player" then teleportTo(getPlayerList()[1])
            elseif feat=="Teleport Random" then teleportTo(getRandomPlayer())
            elseif feat=="Teleport Spawn" then teleportToSpawn()
            elseif feat=="Teleport House" then teleportToHouse()
            elseif feat=="Teleport Police" then teleportToPolice()
            elseif feat=="Teleport Hospital" then teleportToHospital()
            elseif feat=="Teleport School" then teleportToSchool()
            elseif feat=="Teleport Bank" then teleportToBank()
            elseif feat=="Teleport Map Center" then teleportToCenter()
            elseif feat=="Save Position" then savePosition()
            elseif feat=="Load Position" then loadPosition()
            end
        end)
    end
end

if tabFrames["Fun"] then
    local features={
        "Big Head","Small Head","Spin Character","Rainbow Body","Dance",
        "Lay Down","Fake Lag","Random Emote","Crazy Walk","Jump Loop","Reset Fun"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Fun"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Big Head" then setHeadScale(2)
            elseif feat=="Small Head" then setHeadScale(0.5)
            elseif feat=="Spin Character" then spinChar()
            elseif feat=="Rainbow Body" then toggleRainbow()
            elseif feat=="Dance" then playDance()
            elseif feat=="Lay Down" then layDown()
            elseif feat=="Fake Lag" then toggleFakeLag()
            elseif feat=="Random Emote" then randomEmote()
            elseif feat=="Crazy Walk" then toggleCrazyWalk()
            elseif feat=="Jump Loop" then toggleJumpLoop()
            elseif feat=="Reset Fun" then resetFun()
            end
        end)
    end
end

if tabFrames["Misc"] then
    local features={
        "FPS Boost","Rejoin Server","Server Hop","Anti AFK","Auto Click",
        "Day/Night","Clear Fog","Music Player","Notification Test","Copy JobId","Reset Misc"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Misc"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="FPS Boost" then fpsBoost()
            elseif feat=="Rejoin Server" then rejoin()
            elseif feat=="Server Hop" then serverHop()
            elseif feat=="Anti AFK" then toggleAntiAfk()
            elseif feat=="Auto Click" then toggleAutoClick()
            elseif feat=="Day/Night" then toggleDayNight()
            elseif feat=="Clear Fog" then clearFog()
            elseif feat=="Music Player" then toggleMusic()
            elseif feat=="Notification Test" then notify("Test","Hello")
            elseif feat=="Copy JobId" then copyJobId()
            elseif feat=="Reset Misc" then resetMisc()
            end
        end)
    end
endif tabFrames["Troll"] then
    local features={
        "Fling Player","Annoy Sound","Freeze Player","Unfreeze Player",
        "Fake Kick","Spam Chat","Bring Player","Loop Bring",
        "Invisible","Visible","Reset Troll"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Troll"]
        fbtn.MouseButton1Click:Connect(function()
            local target=getPlayerList()[1]
            if not target then return end
            if feat=="Fling Player" then fling(target)
            elseif feat=="Annoy Sound" then annoySound(target)
            elseif feat=="Freeze Player" then freeze(target,true)
            elseif feat=="Unfreeze Player" then freeze(target,false)
            elseif feat=="Fake Kick" then fakeKick(target)
            elseif feat=="Spam Chat" then spamChat(target)
            elseif feat=="Bring Player" then bring(target)
            elseif feat=="Loop Bring" then toggleLoopBring(target)
            elseif feat=="Invisible" then setInvisible(LocalPlayer,true)
            elseif feat=="Visible" then setInvisible(LocalPlayer,false)
            elseif feat=="Reset Troll" then resetTroll()
            end
        end)
    end
end

if tabFrames["Fly"] then
    local features={
        "Fly","Unfly","Fly Speed +","Fly Speed -","Hover",
        "Fly Up","Fly Down","No Gravity",
        "Gravity Normal","Air Walk","Reset Fly"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Fly"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Fly" then startFly()
            elseif feat=="Unfly" then stopFly()
            elseif feat=="Fly Speed +" then changeFlySpeed(1)
            elseif feat=="Fly Speed -" then changeFlySpeed(-1)
            elseif feat=="Hover" then toggleHover()
            elseif feat=="Fly Up" then flyUp()
            elseif feat=="Fly Down" then flyDown()
            elseif feat=="No Gravity" then setGravity(0)
            elseif feat=="Gravity Normal" then setGravity(196.2)
            elseif feat=="Air Walk" then toggleAirWalk()
            elseif feat=="Reset Fly" then resetFly()
            end
        end)
    end
end

if tabFrames["Vehicles"] then
    local features={
        "Spawn Car","Spawn Bike","Spawn Helicopter","Spawn Plane",
        "Delete Vehicle","Vehicle Speed +","Vehicle Speed -","God Vehicle",
        "Ungod Vehicle","Flip Vehicle","Reset Vehicle"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Vehicles"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Spawn Car" then spawnVehicle("Car")
            elseif feat=="Spawn Bike" then spawnVehicle("Bike")
            elseif feat=="Spawn Helicopter" then spawnVehicle("Helicopter")
            elseif feat=="Spawn Plane" then spawnVehicle("Plane")
            elseif feat=="Delete Vehicle" then deleteVehicle()
            elseif feat=="Vehicle Speed +" then vehicleSpeed(1)
            elseif feat=="Vehicle Speed -" then vehicleSpeed(-1)
            elseif feat=="God Vehicle" then godVehicle(true)
            elseif feat=="Ungod Vehicle" then godVehicle(false)
            elseif feat=="Flip Vehicle" then flipVehicle()
            elseif feat=="Reset Vehicle" then resetVehicle()
            end
        end)
    end
end

if tabFrames["Emotes"] then
    local features={
        "Wave","Laugh","Cry","Angry","Clap",
        "Sit Emote","Lay Emote","Point",
        "Cheer","Salute","Reset Emotes"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Emotes"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Wave" then playEmote("Wave")
            elseif feat=="Laugh" then playEmote("Laugh")
            elseif feat=="Cry" then playEmote("Cry")
            elseif feat=="Angry" then playEmote("Angry")
            elseif feat=="Clap" then playEmote("Clap")
            elseif feat=="Sit Emote" then playEmote("Sit")
            elseif feat=="Lay Emote" then playEmote("Lay")
            elseif feat=="Point" then playEmote("Point")
            elseif feat=="Cheer" then playEmote("Cheer")
            elseif feat=="Salute" then playEmote("Salute")
            elseif feat=="Reset Emotes" then stopEmotes()
            end
        end)
    end
endif tabFrames["Visuals"] then
    local features={
        "Full Bright","Normal Light","ESP Players","ESP Houses","ESP Vehicles",
        "Remove Fog","Restore Fog","Change FOV","Reset FOV",
        "Night Vision","Reset Visuals"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Visuals"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Full Bright" then setFullBright(true)
            elseif feat=="Normal Light" then setFullBright(false)
            elseif feat=="ESP Players" then toggleESP("Players")
            elseif feat=="ESP Houses" then toggleESP("Houses")
            elseif feat=="ESP Vehicles" then toggleESP("Vehicles")
            elseif feat=="Remove Fog" then removeFog()
            elseif feat=="Restore Fog" then restoreFog()
            elseif feat=="Change FOV" then setFOV(tonumber(promptUser("FOV")) or 70)
            elseif feat=="Reset FOV" then setFOV(70)
            elseif feat=="Night Vision" then toggleNightVision()
            elseif feat=="Reset Visuals" then resetVisuals()
            end
        end)
    end
end

if tabFrames["Games"] then
    local features={
        "Hide & Seek","Tag Mode","Race Mode","Freeze Tag","Simon Says",
        "Dance Game","Random Challenge","Truth or Dare",
        "Obstacle Run","AFK Game","Reset Games"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Games"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Hide & Seek" then startGame("HideSeek")
            elseif feat=="Tag Mode" then startGame("Tag")
            elseif feat=="Race Mode" then startGame("Race")
            elseif feat=="Freeze Tag" then startGame("FreezeTag")
            elseif feat=="Simon Says" then startGame("Simon")
            elseif feat=="Dance Game" then startGame("Dance")
            elseif feat=="Random Challenge" then startRandomChallenge()
            elseif feat=="Truth or Dare" then startTruthDare()
            elseif feat=="Obstacle Run" then startGame("Obstacle")
            elseif feat=="AFK Game" then startAFKGame()
            elseif feat=="Reset Games" then resetGames()
            end
        end)
    end
endif tabFrames["Teleport"] then
    local features={
        "Teleport to Player","Teleport Random","Teleport Spawn","Teleport House",
        "Teleport Police","Teleport Hospital","Teleport School","Teleport Bank",
        "Teleport Club","Teleport Car","Reset Teleport"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Teleport"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Teleport to Player" then
                local p=selectPlayer()
                if p then teleportTo(p) end
            elseif feat=="Teleport Random" then teleportToRandom()
            elseif feat=="Teleport Spawn" then teleportToCFrame(CFrame.new(0,5,0))
            elseif feat=="Teleport House" then teleportToLocation("House")
            elseif feat=="Teleport Police" then teleportToLocation("Police")
            elseif feat=="Teleport Hospital" then teleportToLocation("Hospital")
            elseif feat=="Teleport School" then teleportToLocation("School")
            elseif feat=="Teleport Bank" then teleportToLocation("Bank")
            elseif feat=="Teleport Club" then teleportToLocation("Club")
            elseif feat=="Teleport Car" then teleportToNearestCar()
            elseif feat=="Reset Teleport" then resetTeleport()
            end
        end)
    end
end

if tabFrames["Fun"] then
    local features={
        "Dance","Laugh","Ragdoll","Spin","Flip",
        "Confetti","Fireworks","Funny Walk","Big Head",
        "Tiny Head","Reset Fun"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Fun"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Dance" then playEmote("Dance")
            elseif feat=="Laugh" then playEmote("Laugh")
            elseif feat=="Ragdoll" then toggleRagdoll()
            elseif feat=="Spin" then spinPlayer()
            elseif feat=="Flip" then flipPlayer()
            elseif feat=="Confetti" then spawnConfetti()
            elseif feat=="Fireworks" then spawnFireworks()
            elseif feat=="Funny Walk" then toggleFunnyWalk()
            elseif feat=="Big Head" then setHeadScale(2)
            elseif feat=="Tiny Head" then setHeadScale(0.5)
            elseif feat=="Reset Fun" then resetFun()
            end
        end)
    end
end

if tabFrames["Troll"] then
    local features={
        "Fake Kick","Fake Ban","Jumpscare","Shake Screen","Invert Controls",
        "Freeze Player","Unfreeze Player","Sound Spam","Teleport Loop",
        "Ragdoll Loop","Reset Trolls"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Troll"]
        fbtn.MouseButton1Click:Connect(function()
            local p=selectPlayer()
            if feat=="Fake Kick" then fakeKick(p)
            elseif feat=="Fake Ban" then fakeBan(p)
            elseif feat=="Jumpscare" then jumpscare(p)
            elseif feat=="Shake Screen" then shakeScreen(p)
            elseif feat=="Invert Controls" then invertControls(p)
            elseif feat=="Freeze Player" then freezePlayer(p)
            elseif feat=="Unfreeze Player" then unfreezePlayer(p)
            elseif feat=="Sound Spam" then soundSpam(p)
            elseif feat=="Teleport Loop" then teleportLoop(p)
            elseif feat=="Ragdoll Loop" then ragdollLoop(p)
            elseif feat=="Reset Trolls" then resetTrolls()
            end
        end)
    end
endlocal Players=game:GetService("Players")
local LocalPlayer=Players.LocalPlayer
local RunService=game:GetService("RunService")
local UIS=game:GetService("UserInputService")
local Workspace=game:GetService("Workspace")
local gui=Instance.new("ScreenGui")
gui.Parent=game.CoreGui
local xButton=Instance.new("TextButton")
xButton.Size=UDim2.new(0,30,0,30)
xButton.Position=UDim2.new(0.5,-15,0,50)
xButton.BackgroundColor3=Color3.new(0,0,0)
xButton.Text=""
xButton.Active=true
xButton.Draggable=true
xButton.Parent=gui
local frame=Instance.new("Frame")
frame.Size=UDim2.new(0,450,0,500)
frame.Position=UDim2.new(0.25,0,0.15,0)
frame.BackgroundColor3=Color3.fromRGB(30,30,30)
frame.Visible=false
frame.Active=true
frame.Draggable=true
frame.Parent=gui
xButton.MouseButton1Click:Connect(function() frame.Visible=not frame.Visible end)
local tabFrame=Instance.new("Frame")
tabFrame.Size=UDim2.new(0,120,1,0)
tabFrame.Position=UDim2.new(0,0,0,0)
tabFrame.BackgroundColor3=Color3.fromRGB(40,40,40)
tabFrame.Parent=frame
local tabs={"Skins","Teleport","Fliegen","Fun","Player","Admin","Tools","Misc","Trollen","Spiele","Weapons"}
local tabFrames={}
local flying=false
local flySpeed=50
local bodyVelocity,bodyGyro
local noclip=false
local function getPlayerList()
    local list={}
    for _,p in pairs(Players:GetPlayers()) do
        if p~=LocalPlayer then
            table.insert(list,p)
        end
    end
    return list
end
local function teleportTo(player)
    if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        LocalPlayer.Character.HumanoidRootPart.CFrame=player.Character.HumanoidRootPart.CFrame+Vector3.new(0,5,0)
    end
end
local function startFly()
    if flying then return end
    local hrp=LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if hrp then
        flying=true
        bodyVelocity=Instance.new("BodyVelocity")
        bodyVelocity.MaxForce=Vector3.new(400000,400000,400000)
        bodyVelocity.Velocity=Vector3.new(0,0,0)
        bodyVelocity.Parent=hrp
        bodyGyro=Instance.new("BodyGyro")
        bodyGyro.MaxTorque=Vector3.new(400000,400000,400000)
        bodyGyro.CFrame=hrp.CFrame
        bodyGyro.Parent=hrp
        RunService.RenderStepped:Connect(function()
            if not flying then return end
            local cam=Workspace.CurrentCamera
            local dir=Vector3.new(0,0,0)
            if UIS:IsKeyDown(Enum.KeyCode.W) then dir=dir+cam.CFrame.LookVector end
            if UIS:IsKeyDown(Enum.KeyCode.S) then dir=dir-cam.CFrame.LookVector end
            if UIS:IsKeyDown(Enum.KeyCode.A) then dir=dir-cam.CFrame.RightVector end
            if UIS:IsKeyDown(Enum.KeyCode.D) then dir=dir+cam.CFrame.RightVector end
            bodyVelocity.Velocity=dir*flySpeed
            bodyGyro.CFrame=cam.CFrame
        end)
    end
end
local function stopFly()
    flying=false
    if bodyVelocity then bodyVelocity:Destroy() end
    if bodyGyro then bodyGyro:Destroy() end
end
RunService.Stepped:Connect(function()
    if noclip and LocalPlayer.Character then
        for _,part in pairs(LocalPlayer.Character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide=false
            end
        end
    end
end)
local function setSize(scale)
    local char=LocalPlayer.Character
    if char and char:FindFirstChild("Humanoid") then
        char.Humanoid.BodyHeightScale.Value=scale
        char.Humanoid.BodyWidthScale.Value=scale
        char.Humanoid.BodyDepthScale.Value=scale
        char.Humanoid.HeadScale.Value=scale
    end
end
local function copySkin(player)
    if player and player.Character then
        local char=LocalPlayer.Character
        for _,part in pairs(player.Character:GetChildren()) do
            if part:IsA("Accessory") or part:IsA("Hat") then
                part:Clone().Parent=char
            end
        end
    end
end
for i,tabName in ipairs(tabs) do
    local btn=Instance.new("TextButton")
    btn.Size=UDim2.new(1,-10,0,35)
    btn.Position=UDim2.new(0,5,0,(i-1)*40+5)
    btn.BackgroundColor3=Color3.fromRGB(60,60,60)
    btn.TextColor3=Color3.new(1,1,1)
    btn.Text=tabName
    btn.Parent=tabFrame
    local contentFrame=Instance.new("Frame")
    contentFrame.Size=UDim2.new(0,310,1,0)
    contentFrame.Position=UDim2.new(0.28,0,0,0)
    contentFrame.BackgroundColor3=Color3.fromRGB(50,50,50)
    contentFrame.Visible=false
    contentFrame.Parent=frame
    local scroll=Instance.new("ScrollingFrame")
    scroll.Size=UDim2.new(1,-10,1,-10)
    scroll.Position=UDim2.new(0,5,0,5)
    scroll.CanvasSize=UDim2.new(0,0,0,500)
    scroll.ScrollBarThickness=8
    scroll.BackgroundTransparency=1
    scroll.Parent=contentFrame
    tabFrames[tabName]=contentFrame
    btn.MouseButton1Click:Connect(function()
        for _,f in pairs(tabFrames) do f.Visible=false end
        contentFrame.Visible=true
    end)
end
-- Skins Tab Buttons
if tabFrames["Skins"] then
    local features={"Copy Skin","Mini Size","Big Size","Random Skin","Change Hat","Change Shirt","Change Pants","Change Accessory","Reset Skin","Apply Player Skin","Full Body Clone"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Skins"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Copy Skin" then
                local list=getPlayerList()
                if #list>0 then copySkin(list[1]) end
            elseif feat=="Mini Size" then
                setSize(0.5)
            elseif feat=="Big Size" then
                setSize(3)
            elseif feat=="Random Skin" then
                local list=getPlayerList()
                if #list>0 then copySkin(list[math.random(1,#list)]) end
            elseif feat=="Change Hat" then
                local list=getPlayerList()
                if #list>0 and list[1].Character then
                    for _,part in pairs(list[1].Character:GetChildren()) do
                        if part:IsA("Hat") then part:Clone().Parent=LocalPlayer.Character end
                    end
                end
            elseif feat=="Change Shirt" then
                local shirt=LocalPlayer.Character:FindFirstChildOfClass("Shirt")
                if shirt then shirt.ShirtTemplate="rbxassetid://12345678" end
            elseif feat=="Change Pants" then
                local pants=LocalPlayer.Character:FindFirstChildOfClass("Pants")
                if pants then pants.PantsTemplate="rbxassetid://12345678" end
            elseif feat=="Change Accessory" then
                local list=getPlayerList()
                if #list>0 then
                    for _,part in pairs(list[1].Character:GetChildren()) do
                        if part:IsA("Accessory") then part:Clone().Parent=LocalPlayer.Character end
                    end
                end
            elseif feat=="Reset Skin" then
                LocalPlayer.Character:BreakJoints()
            elseif feat=="Apply Player Skin" then
                local list=getPlayerList()
                if #list>0 then copySkin(list[1]) end
            elseif feat=="Full Body Clone" then
                local list=getPlayerList()
                if #list>0 then
                    for _,part in pairs(list[1].Character:GetChildren()) do
                        if part:IsA("BasePart") or part:IsA("Accessory") then
                            part:Clone().Parent=LocalPlayer.Character
                        end
                    end
                end
            end
        end)
    end
end
-- Teleport Tab Buttons
if tabFrames["Teleport"] then
    local features={"Teleport to Player","Random Teleport","Spawn","House","School","Police","Hospital","Park","Shop","Bank","Parking"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Teleport"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Teleport to Player" then
                local list=getPlayerList()
                if #list>0 then teleportTo(list[1]) end
            elseif feat=="Random Teleport" then
                local list=getPlayerList()
                if #list>0 then teleportTo(list[math.random(1,#list)]) end
            elseif feat=="Spawn" and Workspace:FindFirstChild("SpawnLocation") then
                LocalPlayer.Character.HumanoidRootPart.CFrame=Workspace.SpawnLocation.CFrame+Vector3.new(0,5,0)
            elseif feat=="House" and Workspace:FindFirstChild("House") then
                LocalPlayer.Character.HumanoidRootPart.CFrame=Workspace.House.CFrame+Vector3.new(0,5,0)
            elseif feat=="School" and Workspace:FindFirstChild("School") then
                LocalPlayer.Character.HumanoidRootPart.CFrame=Workspace.School.CFrame+Vector3.new(0,5,0)
            elseif feat=="Police" and Workspace:FindFirstChild("Police") then
                LocalPlayer.Character.HumanoidRootPart.CFrame=Workspace.Police.CFrame+Vector3.new(0,5,0)
            elseif feat=="Hospital" and Workspace:FindFirstChild("Hospital") then
                LocalPlayer.Character.HumanoidRootPart.CFrame=Workspace.Hospital.CFrame+Vector3.new(0,5,0)
            elseif feat=="Park" and Workspace:FindFirstChild("Park") then
                LocalPlayer.Character.HumanoidRootPart.CFrame=Workspace.Park.CFrame+Vector3.new(0,5,0)
            elseif feat=="Shop" and Workspace:FindFirstChild("Shop") then
                LocalPlayer.Character.HumanoidRootPart.CFrame=Workspace.Shop.CFrame+Vector3.new(0,5,0)
            elseif feat=="Bank" and Workspace:FindFirstChild("Bank") then
                LocalPlayer.Character.HumanoidRootPart.CFrame=Workspace.Bank.CFrame+Vector3.new(0,5,0)
            elseif feat=="Parking" and Workspace:FindFirstChild("Parking") then
                LocalPlayer.Character.HumanoidRootPart.CFrame=Workspace.Parking.CFrame+Vector3.new(0,5,0)
            end
        end)
    end
endif tabFrames["Fliegen"] then
    local features={"Start Fly","Stop Fly","Increase Speed","Decrease Speed","Toggle Noclip","Enable Gravity","Disable Gravity","Hover","Fly Up","Fly Down","Reset Fly"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Fliegen"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Start Fly" then startFly()
            elseif feat=="Stop Fly" then stopFly()
            elseif feat=="Increase Speed" then flySpeed=flySpeed+10
            elseif feat=="Decrease Speed" then flySpeed=math.max(10,flySpeed-10)
            elseif feat=="Toggle Noclip" then noclip=not noclip
            elseif feat=="Enable Gravity" then LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity=Vector3.new(0,0,0); LocalPlayer.Character.Humanoid.UseJumpPower=true
            elseif feat=="Disable Gravity" then LocalPlayer.Character.HumanoidRootPart.AssemblyLinearVelocity=Vector3.new(0,0,0); LocalPlayer.Character.Humanoid.UseJumpPower=false
            elseif feat=="Hover" then if bodyVelocity then bodyVelocity.Velocity=Vector3.new(0,0,0) end
            elseif feat=="Fly Up" then if bodyVelocity then bodyVelocity.Velocity=bodyVelocity.Velocity+Vector3.new(0,flySpeed,0) end
            elseif feat=="Fly Down" then if bodyVelocity then bodyVelocity.Velocity=bodyVelocity.Velocity-Vector3.new(0,flySpeed,0) end
            elseif feat=="Reset Fly" then stopFly()
            end
        end)
    end
end

-- Fun Tab Buttons
if tabFrames["Fun"] then
    local features={"Super Jump","Sit","Dance","Wave","Laugh","Spin","Grow","Shrink","Explode","Speed Boost","Reset Fun"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Fun"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Super Jump" then LocalPlayer.Character.Humanoid.JumpPower=200; LocalPlayer.Character.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            elseif feat=="Sit" then LocalPlayer.Character.Humanoid.Sit=true
            elseif feat=="Dance" then LocalPlayer.Character.Humanoid:LoadAnimation(Instance.new("Animation",LocalPlayer.Character)).AnimationId="rbxassetid://123456"
            elseif feat=="Wave" then LocalPlayer.Character.Humanoid:LoadAnimation(Instance.new("Animation",LocalPlayer.Character)).AnimationId="rbxassetid://123457"
            elseif feat=="Laugh" then LocalPlayer.Character.Humanoid:LoadAnimation(Instance.new("Animation",LocalPlayer.Character)).AnimationId="rbxassetid://123458"
            elseif feat=="Spin" then LocalPlayer.Character.HumanoidRootPart.CFrame=LocalPlayer.Character.HumanoidRootPart.CFrame*CFrame.Angles(0,math.rad(360),0)
            elseif feat=="Grow" then setSize(2)
            elseif feat=="Shrink" then setSize(0.5)
            elseif feat=="Explode" then LocalPlayer.Character.HumanoidRootPart:BreakJoints()
            elseif feat=="Speed Boost" then LocalPlayer.Character.Humanoid.WalkSpeed=50
            elseif feat=="Reset Fun" then LocalPlayer.Character.Humanoid.WalkSpeed=16; setSize(1)
            end
        end)
    end
endif tabFrames["Player"] then
    local features={"View Player","Kick Player","Ban Player","Teleport to Player","Follow Player","Copy Skin","Change Name","Freeze Player","Unfreeze Player","Give Tool","Reset Player"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Player"]
        fbtn.MouseButton1Click:Connect(function()
            local list=getPlayerList()
            local target=list[1]
            if feat=="View Player" and target then
                Workspace.CurrentCamera.CameraSubject=target.Character.Humanoid
            elseif feat=="Kick Player" and target then
                if target~=LocalPlayer then target:Kick("Kicked by hub") end
            elseif feat=="Ban Player" and target then
                if target~=LocalPlayer then target:Kick("Banned by hub") end
            elseif feat=="Teleport to Player" and target then
                teleportTo(target)
            elseif feat=="Follow Player" and target then
                RunService.RenderStepped:Connect(function()
                    if target.Character and LocalPlayer.Character then
                        LocalPlayer.Character.HumanoidRootPart.CFrame=target.Character.HumanoidRootPart.CFrame+Vector3.new(0,5,0)
                    end
                end)
            elseif feat=="Copy Skin" and target then
                copySkin(target)
            elseif feat=="Change Name" and target then
                target.Name="Player_"..math.random(1,1000)
            elseif feat=="Freeze Player" and target and target.Character then
                for _,part in pairs(target.Character:GetDescendants()) do
                    if part:IsA("BasePart") then part.Anchored=true end
                end
            elseif feat=="Unfreeze Player" and target and target.Character then
                for _,part in pairs(target.Character:GetDescendants()) do
                    if part:IsA("BasePart") then part.Anchored=false end
                end
            elseif feat=="Give Tool" and target then
                local tool=Instance.new("Tool")
                tool.Name="HubTool"
                tool.Parent=target.Backpack
            elseif feat=="Reset Player" and target and target.Character then
                target.Character:BreakJoints()
            end
        end)
    end
end

if tabFrames["Admin"] then
    local features={"Kick All","Teleport All to Me","Give Admin Tool","Reset All","Freeze All","Unfreeze All","Server Shutdown","Change Weather","Give Money","Remove Tools","Ban All"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Admin"]
        fbtn.MouseButton1Click:Connect(function()
            local list=getPlayerList()
            if feat=="Kick All" then
                for _,p in pairs(list) do if p~=LocalPlayer then p:Kick("Kicked by hub") end end
            elseif feat=="Teleport All to Me" then
                for _,p in pairs(list) do teleportTo(LocalPlayer) end
            elseif feat=="Give Admin Tool" then
                for _,p in pairs(list) do
                    local tool=Instance.new("Tool")
                    tool.Name="AdminTool"
                    tool.Parent=p.Backpack
                end
            elseif feat=="Reset All" then
                for _,p in pairs(list) do if p.Character then p.Character:BreakJoints() end end
            elseif feat=="Freeze All" then
                for _,p in pairs(list) do if p.Character then for _,part in pairs(p.Character:GetDescendants()) do if part:IsA("BasePart") then part.Anchored=true end end end end
            elseif feat=="Unfreeze All" then
                for _,p in pairs(list) do if p.Character then for _,part in pairs(p.Character:GetDescendants()) do if part:IsA("BasePart") then part.Anchored=false end end end end
            elseif feat=="Server Shutdown" then
                for _,p in pairs(list) do if p~=LocalPlayer then p:Kick("Server shutting down") end end
            elseif feat=="Change Weather" then
                if Workspace:FindFirstChild("Lighting") then Workspace.Lighting.FogEnd=1000 end
            elseif feat=="Give Money" then
                -- Placeholder: integrate with game currency
            elseif feat=="Remove Tools" then
                for _,p in pairs(list) do for _,tool in pairs(p.Backpack:GetChildren()) do tool:Destroy() end end
            elseif feat=="Ban All" then
                for _,p in pairs(list) do if p~=LocalPlayer then p:Kick("Banned by hub") end end
            end
        end)
    end
endif tabFrames["Teleport"] then
    local features={"Teleport to Player","Teleport to Random","Teleport to Spawn","Teleport to Admin","Teleport Up","Teleport Down","Teleport Forward","Teleport Backward","Teleport to Tool","Teleport to Item","Reset Teleport"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Teleport"]
        fbtn.MouseButton1Click:Connect(function()
            local list=getPlayerList()
            local target=list[1]
            if feat=="Teleport to Player" and target then teleportTo(target)
            elseif feat=="Teleport to Random" and #list>0 then teleportTo(list[math.random(1,#list)])
            elseif feat=="Teleport to Spawn" then LocalPlayer.Character.HumanoidRootPart.CFrame=CFrame.new(Vector3.new(0,5,0))
            elseif feat=="Teleport to Admin" and target then teleportTo(LocalPlayer)
            elseif feat=="Teleport Up" then LocalPlayer.Character.HumanoidRootPart.CFrame=LocalPlayer.Character.HumanoidRootPart.CFrame+CFrame.new(0,50,0)
            elseif feat=="Teleport Down" then LocalPlayer.Character.HumanoidRootPart.CFrame=LocalPlayer.Character.HumanoidRootPart.CFrame+CFrame.new(0,-50,0)
            elseif feat=="Teleport Forward" then
                local hrp=LocalPlayer.Character.HumanoidRootPart
                hrp.CFrame=hrp.CFrame*CFrame.new(0,0,-50)
            elseif feat=="Teleport Backward" then
                local hrp=LocalPlayer.Character.HumanoidRootPart
                hrp.CFrame=hrp.CFrame*CFrame.new(0,0,50)
            elseif feat=="Teleport to Tool" then
                local tool=workspace:FindFirstChildWhichIsA("Tool")
                if tool then LocalPlayer.Character.HumanoidRootPart.CFrame=tool.Handle.CFrame end
            elseif feat=="Teleport to Item" then
                local item=workspace:FindFirstChild("Item")
                if item then LocalPlayer.Character.HumanoidRootPart.CFrame=item.CFrame end
            elseif feat=="Reset Teleport" then LocalPlayer.Character.HumanoidRootPart.CFrame=CFrame.new(Vector3.new(0,5,0))
            end
        end)
    end
end

if tabFrames["Skins"] then
    local features={"Copy Skin","Change Skin Color","Random Skin","Mini Size","Max Size","Reset Size","Set Custom Size","Equip Hat","Remove Hat","Equip Shirt","Equip Pants"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Skins"]
        fbtn.MouseButton1Click:Connect(function()
            local char=LocalPlayer.Character
            if feat=="Copy Skin" then
                copySkin(LocalPlayer)
            elseif feat=="Change Skin Color" then
                for _,part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then part.BrickColor=BrickColor.Random() end
                end
            elseif feat=="Random Skin" then
                for _,part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then part.BrickColor=BrickColor.Random() end
                end
            elseif feat=="Mini Size" then setSize(0.5)
            elseif feat=="Max Size" then setSize(1000)
            elseif feat=="Reset Size" then setSize(1)
            elseif feat=="Set Custom Size" then
                local input=tonumber(promptUser("Enter size 0.1-1000"))
                if input then setSize(input) end
            elseif feat=="Equip Hat" then
                local hat=Instance.new("Accessory")
                hat.Name="HubHat"
                hat.Parent=char
            elseif feat=="Remove Hat" then
                for _,acc in pairs(char:GetChildren()) do if acc:IsA("Accessory") then acc:Destroy() end end
            elseif feat=="Equip Shirt" then
                local shirt=Instance.new("Shirt")
                shirt.ShirtTemplate="rbxassetid://123456"
                shirt.Parent=char
            elseif feat=="Equip Pants" then
                local pants=Instance.new("Pants")
                pants.PantsTemplate="rbxassetid://123457"
                pants.Parent=char
            end
        end)
    end
endif tabFrames["Troll"] then
    local features={"Spin Player","Freeze Player","Launch Player","Change WalkSpeed","Change JumpPower","Invisible Player","Clone Player","Shrink Player","Grow Player","Explode Player","Reset Player"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Troll"]
        fbtn.MouseButton1Click:Connect(function()
            local list=getPlayerList()
            local target=list[1]
            if not target then return end
            local char=target.Character
            if feat=="Spin Player" and char then
                RunService.RenderStepped:Connect(function()
                    char.HumanoidRootPart.CFrame=char.HumanoidRootPart.CFrame*CFrame.Angles(0,math.rad(30),0)
                end)
            elseif feat=="Freeze Player" and char then
                for _,part in pairs(char:GetDescendants()) do if part:IsA("BasePart") then part.Anchored=true end end
            elseif feat=="Launch Player" and char then
                char.HumanoidRootPart.Velocity=Vector3.new(0,100,0)
            elseif feat=="Change WalkSpeed" and char then
                char.Humanoid.WalkSpeed=50
            elseif feat=="Change JumpPower" and char then
                char.Humanoid.JumpPower=200
            elseif feat=="Invisible Player" and char then
                for _,part in pairs(char:GetDescendants()) do if part:IsA("BasePart") then part.Transparency=1 end end
            elseif feat=="Clone Player" and char then
                local clone=char:Clone()
                clone.Parent=workspace
                clone:SetPrimaryPartCFrame(char.HumanoidRootPart.CFrame + Vector3.new(5,0,0))
            elseif feat=="Shrink Player" and char then
                setSizeChar(char,0.5)
            elseif feat=="Grow Player" and char then
                setSizeChar(char,5)
            elseif feat=="Explode Player" and char then
                char:BreakJoints()
            elseif feat=="Reset Player" and char then
                setSizeChar(char,1)
                for _,part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.Anchored=false
                        part.Transparency=0
                    end
                end
                char.Humanoid.WalkSpeed=16
                char.Humanoid.JumpPower=50
            end
        end)
    end
end

if tabFrames["Games"] then
    local features={"Start Minigame","End Minigame","Give Points","Reset Points","Teleport to Game","Random Game Event","Change Game Map","Spawn Game Items","Remove Game Items","Enable Game Boost","Reset Game"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Games"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Start Minigame" then startMinigame()
            elseif feat=="End Minigame" then endMinigame()
            elseif feat=="Give Points" then givePoints(100)
            elseif feat=="Reset Points" then resetPoints()
            elseif feat=="Teleport to Game" then LocalPlayer.Character.HumanoidRootPart.CFrame=CFrame.new(Vector3.new(0,5,0))
            elseif feat=="Random Game Event" then randomEvent()
            elseif feat=="Change Game Map" then changeMap()
            elseif feat=="Spawn Game Items" then spawnItems()
            elseif feat=="Remove Game Items" then removeItems()
            elseif feat=="Enable Game Boost" then enableBoost()
            elseif feat=="Reset Game" then resetGame()
            end
        end)
    end
endif tabFrames["Fly"] then
    local features={"Start Fly","Stop Fly","Fly Up","Fly Down","Fly Forward","Fly Backward","Increase Speed","Decrease Speed","Set Max Height","Set Min Height","Reset Fly"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Fly"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Start Fly" then startFly()
            elseif feat=="Stop Fly" then stopFly()
            elseif feat=="Fly Up" then moveFly(Vector3.new(0,50,0))
            elseif feat=="Fly Down" then moveFly(Vector3.new(0,-50,0))
            elseif feat=="Fly Forward" then
                local hrp=LocalPlayer.Character.HumanoidRootPart
                hrp.CFrame=hrp.CFrame*CFrame.new(0,0,-50)
            elseif feat=="Fly Backward" then
                local hrp=LocalPlayer.Character.HumanoidRootPart
                hrp.CFrame=hrp.CFrame*CFrame.new(0,0,50)
            elseif feat=="Increase Speed" then flySpeed=flySpeed+10
            elseif feat=="Decrease Speed" then flySpeed=math.max(10,flySpeed-10)
            elseif feat=="Set Max Height" then maxFlyHeight=tonumber(promptUser("Enter max height")) or 500
            elseif feat=="Set Min Height" then minFlyHeight=tonumber(promptUser("Enter min height")) or 0
            elseif feat=="Reset Fly" then
                flySpeed=50
                maxFlyHeight=500
                minFlyHeight=0
                stopFly()
            end
        end)
    end
end

if tabFrames["Fun"] then
    local features={"Dance","Sit","Wave","Laugh","Cry","Jump","Spin","Emote1","Emote2","Emote3","Reset Fun"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Fun"]
        fbtn.MouseButton1Click:Connect(function()
            local char=LocalPlayer.Character
            if not char then return end
            if feat=="Dance" then playAnim("Dance")
            elseif feat=="Sit" then playAnim("Sit")
            elseif feat=="Wave" then playAnim("Wave")
            elseif feat=="Laugh" then playAnim("Laugh")
            elseif feat=="Cry" then playAnim("Cry")
            elseif feat=="Jump" then char.Humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            elseif feat=="Spin" then
                RunService.RenderStepped:Connect(function()
                    char.HumanoidRootPart.CFrame=char.HumanoidRootPart.CFrame*CFrame.Angles(0,math.rad(30),0)
                end)
            elseif feat=="Emote1" then playAnim("Emote1")
            elseif feat=="Emote2" then playAnim("Emote2")
            elseif feat=="Emote3" then playAnim("Emote3")
            elseif feat=="Reset Fun" then resetEmotes()
            end
        end)
    end
endif tabFrames["Admin"] then
    local features={"Kick Player","Ban Player","Mute Player","Unmute Player","Reset Player","Give Admin","Remove Admin","Change Rank","Teleport Admin","Spawn Admin Item","Reset Admin Actions"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Admin"]
        fbtn.MouseButton1Click:Connect(function()
            local list=getPlayerList()
            local target=list[1]
            if not target then return end
            if feat=="Kick Player" then kickPlayer(target)
            elseif feat=="Ban Player" then banPlayer(target)
            elseif feat=="Mute Player" then mutePlayer(target)
            elseif feat=="Unmute Player" then unmutePlayer(target)
            elseif feat=="Reset Player" then resetChar(target)
            elseif feat=="Give Admin" then giveAdmin(target)
            elseif feat=="Remove Admin" then removeAdmin(target)
            elseif feat=="Change Rank" then changeRank(target,promptUser("Enter rank"))
            elseif feat=="Teleport Admin" then teleportTo(target)
            elseif feat=="Spawn Admin Item" then spawnAdminItem()
            elseif feat=="Reset Admin Actions" then resetAdminActions()
            end
        end)
    end
end

if tabFrames["Player"] then
    local features={"Reset Character","Change WalkSpeed","Change JumpPower","Toggle Sit","Toggle Crouch","Equip Tool","Unequip Tool","Change Skin","Randomize Appearance","Toggle Noclip","Reset All"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Player"]
        fbtn.MouseButton1Click:Connect(function()
            local char=LocalPlayer.Character
            if not char then return end
            if feat=="Reset Character" then resetChar(LocalPlayer)
            elseif feat=="Change WalkSpeed" then char.Humanoid.WalkSpeed=tonumber(promptUser("Enter WalkSpeed")) or 16
            elseif feat=="Change JumpPower" then char.Humanoid.JumpPower=tonumber(promptUser("Enter JumpPower")) or 50
            elseif feat=="Toggle Sit" then char.Humanoid.Sit=true
            elseif feat=="Toggle Crouch" then toggleCrouch(char)
            elseif feat=="Equip Tool" then equipTool("HubTool")
            elseif feat=="Unequip Tool" then unequipTools()
            elseif feat=="Change Skin" then changeSkin(LocalPlayer)
            elseif feat=="Randomize Appearance" then randomizeAppearance(LocalPlayer)
            elseif feat=="Toggle Noclip" then noclip=not noclip
            elseif feat=="Reset All" then
                resetChar(LocalPlayer)
                char.Humanoid.WalkSpeed=16
                char.Humanoid.JumpPower=50
                noclip=false
            end
        end)
    end
endif tabFrames["Misc"] then
    local features={"Toggle GUI","Enable Night Mode","Disable Night Mode","Show FPS","Hide FPS","Play Sound","Stop Sound","Enable Music","Disable Music","Reset Settings","Random Event"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Misc"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Toggle GUI" then mainGui.Enabled=not mainGui.Enabled
            elseif feat=="Enable Night Mode" then setLighting("Night")
            elseif feat=="Disable Night Mode" then setLighting("Day")
            elseif feat=="Show FPS" then showFPS(true)
            elseif feat=="Hide FPS" then showFPS(false)
            elseif feat=="Play Sound" then playSound("HubSound")
            elseif feat=="Stop Sound" then stopSound("HubSound")
            elseif feat=="Enable Music" then enableMusic(true)
            elseif feat=="Disable Music" then enableMusic(false)
            elseif feat=="Reset Settings" then resetAllSettings()
            elseif feat=="Random Event" then triggerRandomEvent()
            end
        end)
    end
end

if tabFrames["Settings"] then
    local features={"Change Keybinds","Reset Keybinds","Change UI Color","Reset UI Color","Toggle Notifications","Reset Notifications","Change Volume","Reset Volume","Enable Auto-Update","Disable Auto-Update","Reset All Settings"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Settings"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Change Keybinds" then changeKeybinds()
            elseif feat=="Reset Keybinds" then resetKeybinds()
            elseif feat=="Change UI Color" then changeUIColor(promptUser("Enter Color Hex"))
            elseif feat=="Reset UI Color" then resetUIColor()
            elseif feat=="Toggle Notifications" then toggleNotifications()
            elseif feat=="Reset Notifications" then resetNotifications()
            elseif feat=="Change Volume" then setVolume(tonumber(promptUser("Enter Volume 0-100")))
            elseif feat=="Reset Volume" then resetVolume()
            elseif feat=="Enable Auto-Update" then autoUpdate=true
            elseif feat=="Disable Auto-Update" then autoUpdate=false
            elseif feat=="Reset All Settings" then resetAllSettings()
            end
        end)
    end
endbinds","Reset Keybinds","Change UI Color","Reset UI Color","Toggle Notifications","Reset Notifications","Change Volume","Reset Volume","Enable Auto-Update","Disable Auto-Update","Reset All Settings"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Settings"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Change Keybinds" then changeKeybinds()
            elseif feat=="Reset Keybinds" then resetKeybinds()
            elseif feat=="Change UI Color" then changeUIColor(promptUser("Enter Color Hex"))
            elseif feat=="Reset UI Color" then resetUIColor()
            elseif feat=="Toggle Notifications" then toggleNotifications()
            elseif feat=="Reset Notifications" then resetNotifications()
            elseif feat=="Change Volume" then setVolume(tonumber(promptUser("Enter Volume 0-100")))
            elseif feat=="Reset Volume" then resetVolume()
            elseif feat=="Enable Auto-Update" then autoUpdate=true
            elseif feat=="Disable Auto-Update" then autoUpdate=false
            elseif feat=="Reset All Settings" then resetAllSettings()
            end
        end)
    end
end

if tabFrames["Fun"] then
    local features={"Dance","Sit","Wave","Jump","Spin","Laugh","Cry","Clap","Facepalm","Random Emote","Reset Emotes"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Fun"]
        fbtn.MouseButton1Click:Connect(function()
            local char=LocalPlayer.Character
            if not char then return end
            if feat=="Dance" then playEmote("Dance")
            elseif feat=="Sit" then char.Humanoid.Sit=true
            elseif feat=="Wave" then playEmote("Wave")
            elseif feat=="Jump" then char.Humanoid.Jump=true
            elseif feat=="Spin" then spinCharacter(char)
            elseif feat=="Laugh" then playEmote("Laugh")
            elseif feat=="Cry" then playEmote("Cry")
            elseif feat=="Clap" then playEmote("Clap")
            elseif feat=="Facepalm" then playEmote("Facepalm")
            elseif feat=="Random Emote" then playRandomEmote()
            elseif feat=="Reset Emotes" then resetEmotes()
            end
        end)
    end
endif tabFrames["Fly"] then
    local features={"Enable Fly","Disable Fly","Increase Speed","Decrease Speed","Fly Up","Fly Down","Hover","Reset Fly","Fly To Player","Fly To Random","Fly To Spawn"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Fly"]
        fbtn.MouseButton1Click:Connect(function()
            local char=LocalPlayer.Character
            if not char then return end
            if feat=="Enable Fly" then enableFly(char)
            elseif feat=="Disable Fly" then disableFly(char)
            elseif feat=="Increase Speed" then changeFlySpeed(char,1)
            elseif feat=="Decrease Speed" then changeFlySpeed(char,-1)
            elseif feat=="Fly Up" then flyMove(char,Vector3.new(0,5,0))
            elseif feat=="Fly Down" then flyMove(char,Vector3.new(0,-5,0))
            elseif feat=="Hover" then toggleHover(char)
            elseif feat=="Reset Fly" then resetFly(char)
            elseif feat=="Fly To Player" then flyToPlayer(choosePlayer(getPlayerList()))
            elseif feat=="Fly To Random" then flyToPlayer(getPlayerList()[math.random(#getPlayerList())])
            elseif feat=="Fly To Spawn" then flyToCFrame(char,spawnCFrame)
            end
        end)
    end
end

if tabFrames["Skins"] then
    local features={"Copy Skin","Apply Random Skin","Reset Skin","Change Head","Change Body","Change Arms","Change Legs","Change Hair","Change Hat","Change Face","Reset All Skin"}
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Skins"]
        fbtn.MouseButton1Click:Connect(function()
            local target=choosePlayer(getPlayerList())
            if feat=="Copy Skin" and target then copySkin(target)
            elseif feat=="Apply Random Skin" then randomizeAppearance(LocalPlayer)
            elseif feat=="Reset Skin" then resetAppearance(LocalPlayer)
            elseif feat=="Change Head" then changeBodyPart(LocalPlayer,"Head")
            elseif feat=="Change Body" then changeBodyPart(LocalPlayer,"Torso")
            elseif feat=="Change Arms" then changeBodyPart(LocalPlayer,"Arms")
            elseif feat=="Change Legs" then changeBodyPart(LocalPlayer,"Legs")
            elseif feat=="Change Hair" then changeHair(LocalPlayer)
            elseif feat=="Change Hat" then changeHat(LocalPlayer)
            elseif feat=="Change Face" then changeFace(LocalPlayer)
            elseif feat=="Reset All Skin" then resetAppearance(LocalPlayer)
            end
        end)
    end
endif tabFrames["Misc"] then
    local features={
        "Rejoin Server","Server Hop","Copy JobId","FPS Boost","Anti AFK",
        "Toggle Music","Change FOV","Reset Camera","Hide UI","Show UI",
        "Toggle Shadows","Low Graphics","High Graphics","Toggle Notifications",
        "Clear Workspace","Respawn At Place","Random Teleport","Spin Character",
        "Stop Spin","Float","Stop Float","Reset Misc"
    }
    local spinning=false
    local floating=false
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Misc"]
        fbtn.MouseButton1Click:Connect(function()
            local char=LocalPlayer.Character
            if feat=="Rejoin Server" then
                TeleportService:Teleport(game.PlaceId,LocalPlayer)
            elseif feat=="Server Hop" then
                serverHop()
            elseif feat=="Copy JobId" then
                setclipboard(game.JobId)
            elseif feat=="FPS Boost" then
                setfpscap(999)
            elseif feat=="Anti AFK" then
                antiAfk()
            elseif feat=="Toggle Music" then
                toggleMusic()
            elseif feat=="Change FOV" then
                workspace.CurrentCamera.FieldOfView=tonumber(promptUser("FOV")) or 70
            elseif feat=="Reset Camera" then
                workspace.CurrentCamera.FieldOfView=70
            elseif feat=="Hide UI" then
                mainGui.Enabled=false
            elseif feat=="Show UI" then
                mainGui.Enabled=true
            elseif feat=="Toggle Shadows" then
                Lighting.GlobalShadows=not Lighting.GlobalShadows
            elseif feat=="Low Graphics" then
                setLowGraphics()
            elseif feat=="High Graphics" then
                setHighGraphics()
            elseif feat=="Toggle Notifications" then
                notifications=not notifications
            elseif feat=="Clear Workspace" then
                for _,v in pairs(workspace:GetChildren()) do
                    if v:IsA("Part") and not v.Anchored then v:Destroy() end
                end
            elseif feat=="Respawn At Place" then
                resetChar(LocalPlayer)
            elseif feat=="Random Teleport" and char then
                char:MoveTo(Vector3.new(math.random(-500,500),50,math.random(-500,500)))
            elseif feat=="Spin Character" and char then
                spinning=true
                task.spawn(function()
                    while spinning and char and char:FindFirstChild("HumanoidRootPart") do
                        char.HumanoidRootPart.CFrame*=CFrame.Angles(0,math.rad(10),0)
                        task.wait()
                    end
                end)
            elseif feat=="Stop Spin" then
                spinning=false
            elseif feat=="Float" and char then
                floating=true
                task.spawn(function()
                    while floating and char and char:FindFirstChild("HumanoidRootPart") do
                        char.HumanoidRootPart.Velocity=Vector3.new(0,50,0)
                        task.wait()
                    end
                end)
            elseif feat=="Stop Float" then
                floating=false
            elseif feat=="Reset Misc" then
                spinning=false
                floating=false
                workspace.CurrentCamera.FieldOfView=70
                Lighting.GlobalShadows=true
            end
        end)
    end
endif tabFrames["Visual"] then
    local features={
        "ESP Players","ESP Boxes","ESP Names","ESP Distance","ESP Health",
        "Chams","Remove Chams","Tracers","Crosshair","FOV Circle","Reset Visuals"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Visual"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="ESP Players" then toggleESP()
            elseif feat=="ESP Boxes" then toggleESPBoxes()
            elseif feat=="ESP Names" then toggleESPNames()
            elseif feat=="ESP Distance" then toggleESPDistance()
            elseif feat=="ESP Health" then toggleESPHealth()
            elseif feat=="Chams" then enableChams()
            elseif feat=="Remove Chams" then disableChams()
            elseif feat=="Tracers" then toggleTracers()
            elseif feat=="Crosshair" then toggleCrosshair()
            elseif feat=="FOV Circle" then toggleFOVCircle()
            elseif feat=="Reset Visuals" then resetVisuals()
            end
        end)
    end
end

if tabFrames["Combat"] then
    local features={
        "Aimbot","Silent Aim","Trigger Bot","Auto Shoot","Hitbox Expander",
        "No Recoil","No Spread","Fast Reload","Kill Aura","Auto Farm","Reset Combat"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Combat"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Aimbot" then toggleAimbot()
            elseif feat=="Silent Aim" then toggleSilentAim()
            elseif feat=="Trigger Bot" then toggleTriggerBot()
            elseif feat=="Auto Shoot" then toggleAutoShoot()
            elseif feat=="Hitbox Expander" then toggleHitbox()
            elseif feat=="No Recoil" then toggleNoRecoil()
            elseif feat=="No Spread" then toggleNoSpread()
            elseif feat=="Fast Reload" then toggleFastReload()
            elseif feat=="Kill Aura" then toggleKillAura()
            elseif feat=="Auto Farm" then toggleAutoFarm()
            elseif feat=="Reset Combat" then resetCombat()
            end
        end)
    end
end

if tabFrames["Movement"] then
    local features={
        "Speed Hack","Jump Hack","Fly","Teleport Walk","Dash",
        "Wall Climb","Spider","Bunny Hop","Slide","Auto Sprint","Reset Movement"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Movement"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Speed Hack" then toggleSpeed()
            elseif feat=="Jump Hack" then toggleJump()
            elseif feat=="Fly" then toggleFly()
            elseif feat=="Teleport Walk" then toggleTPWalk()
            elseif feat=="Dash" then dash()
            elseif feat=="Wall Climb" then toggleWallClimb()
            elseif feat=="Spider" then toggleSpider()
            elseif feat=="Bunny Hop" then toggleBhop()
            elseif feat=="Slide" then slide()
            elseif feat=="Auto Sprint" then toggleSprint()
            elseif feat=="Reset Movement" then resetMovement()
            end
        end)
    end
endif tabFrames["Teleport"] then
    local features={
        "Teleport to Player","Teleport Random","Teleport Spawn","Teleport House",
        "Teleport Police","Teleport Hospital","Teleport School","Teleport Bank",
        "Teleport Map Center","Save Position","Load Position"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Teleport"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Teleport to Player" then teleportTo(getPlayerList()[1])
            elseif feat=="Teleport Random" then teleportTo(getRandomPlayer())
            elseif feat=="Teleport Spawn" then teleportToSpawn()
            elseif feat=="Teleport House" then teleportToHouse()
            elseif feat=="Teleport Police" then teleportToPolice()
            elseif feat=="Teleport Hospital" then teleportToHospital()
            elseif feat=="Teleport School" then teleportToSchool()
            elseif feat=="Teleport Bank" then teleportToBank()
            elseif feat=="Teleport Map Center" then teleportToCenter()
            elseif feat=="Save Position" then savePosition()
            elseif feat=="Load Position" then loadPosition()
            end
        end)
    end
end

if tabFrames["Fun"] then
    local features={
        "Big Head","Small Head","Spin Character","Rainbow Body","Dance",
        "Lay Down","Fake Lag","Random Emote","Crazy Walk","Jump Loop","Reset Fun"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Fun"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Big Head" then setHeadScale(2)
            elseif feat=="Small Head" then setHeadScale(0.5)
            elseif feat=="Spin Character" then spinChar()
            elseif feat=="Rainbow Body" then toggleRainbow()
            elseif feat=="Dance" then playDance()
            elseif feat=="Lay Down" then layDown()
            elseif feat=="Fake Lag" then toggleFakeLag()
            elseif feat=="Random Emote" then randomEmote()
            elseif feat=="Crazy Walk" then toggleCrazyWalk()
            elseif feat=="Jump Loop" then toggleJumpLoop()
            elseif feat=="Reset Fun" then resetFun()
            end
        end)
    end
end

if tabFrames["Misc"] then
    local features={
        "FPS Boost","Rejoin Server","Server Hop","Anti AFK","Auto Click",
        "Day/Night","Clear Fog","Music Player","Notification Test","Copy JobId","Reset Misc"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Misc"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="FPS Boost" then fpsBoost()
            elseif feat=="Rejoin Server" then rejoin()
            elseif feat=="Server Hop" then serverHop()
            elseif feat=="Anti AFK" then toggleAntiAfk()
            elseif feat=="Auto Click" then toggleAutoClick()
            elseif feat=="Day/Night" then toggleDayNight()
            elseif feat=="Clear Fog" then clearFog()
            elseif feat=="Music Player" then toggleMusic()
            elseif feat=="Notification Test" then notify("Test","Hello")
            elseif feat=="Copy JobId" then copyJobId()
            elseif feat=="Reset Misc" then resetMisc()
            end
        end)
    end
endif tabFrames["Troll"] then
    local features={
        "Fling Player","Annoy Sound","Freeze Player","Unfreeze Player",
        "Fake Kick","Spam Chat","Bring Player","Loop Bring",
        "Invisible","Visible","Reset Troll"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Troll"]
        fbtn.MouseButton1Click:Connect(function()
            local target=getPlayerList()[1]
            if not target then return end
            if feat=="Fling Player" then fling(target)
            elseif feat=="Annoy Sound" then annoySound(target)
            elseif feat=="Freeze Player" then freeze(target,true)
            elseif feat=="Unfreeze Player" then freeze(target,false)
            elseif feat=="Fake Kick" then fakeKick(target)
            elseif feat=="Spam Chat" then spamChat(target)
            elseif feat=="Bring Player" then bring(target)
            elseif feat=="Loop Bring" then toggleLoopBring(target)
            elseif feat=="Invisible" then setInvisible(LocalPlayer,true)
            elseif feat=="Visible" then setInvisible(LocalPlayer,false)
            elseif feat=="Reset Troll" then resetTroll()
            end
        end)
    end
end

if tabFrames["Fly"] then
    local features={
        "Fly","Unfly","Fly Speed +","Fly Speed -","Hover",
        "Fly Up","Fly Down","No Gravity",
        "Gravity Normal","Air Walk","Reset Fly"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Fly"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Fly" then startFly()
            elseif feat=="Unfly" then stopFly()
            elseif feat=="Fly Speed +" then changeFlySpeed(1)
            elseif feat=="Fly Speed -" then changeFlySpeed(-1)
            elseif feat=="Hover" then toggleHover()
            elseif feat=="Fly Up" then flyUp()
            elseif feat=="Fly Down" then flyDown()
            elseif feat=="No Gravity" then setGravity(0)
            elseif feat=="Gravity Normal" then setGravity(196.2)
            elseif feat=="Air Walk" then toggleAirWalk()
            elseif feat=="Reset Fly" then resetFly()
            end
        end)
    end
end

if tabFrames["Vehicles"] then
    local features={
        "Spawn Car","Spawn Bike","Spawn Helicopter","Spawn Plane",
        "Delete Vehicle","Vehicle Speed +","Vehicle Speed -","God Vehicle",
        "Ungod Vehicle","Flip Vehicle","Reset Vehicle"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Vehicles"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Spawn Car" then spawnVehicle("Car")
            elseif feat=="Spawn Bike" then spawnVehicle("Bike")
            elseif feat=="Spawn Helicopter" then spawnVehicle("Helicopter")
            elseif feat=="Spawn Plane" then spawnVehicle("Plane")
            elseif feat=="Delete Vehicle" then deleteVehicle()
            elseif feat=="Vehicle Speed +" then vehicleSpeed(1)
            elseif feat=="Vehicle Speed -" then vehicleSpeed(-1)
            elseif feat=="God Vehicle" then godVehicle(true)
            elseif feat=="Ungod Vehicle" then godVehicle(false)
            elseif feat=="Flip Vehicle" then flipVehicle()
            elseif feat=="Reset Vehicle" then resetVehicle()
            end
        end)
    end
end

if tabFrames["Emotes"] then
    local features={
        "Wave","Laugh","Cry","Angry","Clap",
        "Sit Emote","Lay Emote","Point",
        "Cheer","Salute","Reset Emotes"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Emotes"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Wave" then playEmote("Wave")
            elseif feat=="Laugh" then playEmote("Laugh")
            elseif feat=="Cry" then playEmote("Cry")
            elseif feat=="Angry" then playEmote("Angry")
            elseif feat=="Clap" then playEmote("Clap")
            elseif feat=="Sit Emote" then playEmote("Sit")
            elseif feat=="Lay Emote" then playEmote("Lay")
            elseif feat=="Point" then playEmote("Point")
            elseif feat=="Cheer" then playEmote("Cheer")
            elseif feat=="Salute" then playEmote("Salute")
            elseif feat=="Reset Emotes" then stopEmotes()
            end
        end)
    end
endif tabFrames["Visuals"] then
    local features={
        "Full Bright","Normal Light","ESP Players","ESP Houses","ESP Vehicles",
        "Remove Fog","Restore Fog","Change FOV","Reset FOV",
        "Night Vision","Reset Visuals"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Visuals"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Full Bright" then setFullBright(true)
            elseif feat=="Normal Light" then setFullBright(false)
            elseif feat=="ESP Players" then toggleESP("Players")
            elseif feat=="ESP Houses" then toggleESP("Houses")
            elseif feat=="ESP Vehicles" then toggleESP("Vehicles")
            elseif feat=="Remove Fog" then removeFog()
            elseif feat=="Restore Fog" then restoreFog()
            elseif feat=="Change FOV" then setFOV(tonumber(promptUser("FOV")) or 70)
            elseif feat=="Reset FOV" then setFOV(70)
            elseif feat=="Night Vision" then toggleNightVision()
            elseif feat=="Reset Visuals" then resetVisuals()
            end
        end)
    end
end

if tabFrames["Games"] then
    local features={
        "Hide & Seek","Tag Mode","Race Mode","Freeze Tag","Simon Says",
        "Dance Game","Random Challenge","Truth or Dare",
        "Obstacle Run","AFK Game","Reset Games"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Games"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Hide & Seek" then startGame("HideSeek")
            elseif feat=="Tag Mode" then startGame("Tag")
            elseif feat=="Race Mode" then startGame("Race")
            elseif feat=="Freeze Tag" then startGame("FreezeTag")
            elseif feat=="Simon Says" then startGame("Simon")
            elseif feat=="Dance Game" then startGame("Dance")
            elseif feat=="Random Challenge" then startRandomChallenge()
            elseif feat=="Truth or Dare" then startTruthDare()
            elseif feat=="Obstacle Run" then startGame("Obstacle")
            elseif feat=="AFK Game" then startAFKGame()
            elseif feat=="Reset Games" then resetGames()
            end
        end)
    end
endif tabFrames["Teleport"] then
    local features={
        "Teleport to Player","Teleport Random","Teleport Spawn","Teleport House",
        "Teleport Police","Teleport Hospital","Teleport School","Teleport Bank",
        "Teleport Club","Teleport Car","Reset Teleport"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Teleport"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Teleport to Player" then
                local p=selectPlayer()
                if p then teleportTo(p) end
            elseif feat=="Teleport Random" then teleportToRandom()
            elseif feat=="Teleport Spawn" then teleportToCFrame(CFrame.new(0,5,0))
            elseif feat=="Teleport House" then teleportToLocation("House")
            elseif feat=="Teleport Police" then teleportToLocation("Police")
            elseif feat=="Teleport Hospital" then teleportToLocation("Hospital")
            elseif feat=="Teleport School" then teleportToLocation("School")
            elseif feat=="Teleport Bank" then teleportToLocation("Bank")
            elseif feat=="Teleport Club" then teleportToLocation("Club")
            elseif feat=="Teleport Car" then teleportToNearestCar()
            elseif feat=="Reset Teleport" then resetTeleport()
            end
        end)
    end
end

if tabFrames["Fun"] then
    local features={
        "Dance","Laugh","Ragdoll","Spin","Flip",
        "Confetti","Fireworks","Funny Walk","Big Head",
        "Tiny Head","Reset Fun"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
        fbtn.Parent=tabFrames["Fun"]
        fbtn.MouseButton1Click:Connect(function()
            if feat=="Dance" then playEmote("Dance")
            elseif feat=="Laugh" then playEmote("Laugh")
            elseif feat=="Ragdoll" then toggleRagdoll()
            elseif feat=="Spin" then spinPlayer()
            elseif feat=="Flip" then flipPlayer()
            elseif feat=="Confetti" then spawnConfetti()
            elseif feat=="Fireworks" then spawnFireworks()
            elseif feat=="Funny Walk" then toggleFunnyWalk()
            elseif feat=="Big Head" then setHeadScale(2)
            elseif feat=="Tiny Head" then setHeadScale(0.5)
            elseif feat=="Reset Fun" then resetFun()
            end
        end)
    end
end

if tabFrames["Troll"] then
    local features={
        "Fake Kick","Fake Ban","Jumpscare","Shake Screen","Invert Controls",
        "Freeze Player","Unfreeze Player","Sound Spam","Teleport Loop",
        "Ragdoll Loop","Reset Trolls"
    }
    for i,feat in ipairs(features) do
        local fbtn=Instance.new("TextButton")
        fbtn.Size=UDim2.new(1,-10,0,35)
        fbtn.Position=UDim2.new(0,5,0,(i-1)*40)
        fbtn.BackgroundColor3=Color3.fromRGB(70,70,70)
        fbtn.TextColor3=Color3.new(1,1,1)
        fbtn.Text=feat
 FBTN. Eltern = TabFrames ["Troll"]
  FBTN. MouseButton1Click:Connect (Funktion ()  
 lokal p=selectPlayer () 
 if feat="Fake Kick" dann fakeKick (p) 
 elseif feat="Fake Ban" dann fakeBan (p) 
 elseif feat="Jumpscare" dann springtcare (p) 
 elseif feat="Shake Screen" dann shakeScreen (p) 
 elseif feat="Invert Controls" dann invertControls (p) 
 elseif feat="Freeze Player" dann freezePlayer (p) 
 elseif feat="Unfreeze Player" dann unfreezePlayer (p) 
 elseif feat="Sound Spam" dann soundSpam (p) 
 elseif feat="Teleport Loop" dann teleportLoop (p) 
   elseif feat="Ragdoll Loop" dann ragdollLoop (p)    
    elseif feat=="Trolle zurücksetzen" und dann die Rolle zurücksetzen ()      
 Ende 
 Ende) . 
 Ende 
Ende
