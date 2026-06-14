local ScreenGui = Instance.new("ScreenGui")
local Main = Instance.new("Frame")
local Stroke = Instance.new("UIStroke")
local Corner = Instance.new("UICorner")
local Title = Instance.new("TextLabel")
local ItemNameBox = Instance.new("TextBox")
local Dupe = Instance.new("TextButton")
local Dupe2x = Instance.new("TextButton")
local Status = Instance.new("TextLabel")
local SelectedItem = Instance.new("TextLabel")
local SearchButton = Instance.new("TextButton")

ScreenGui.Parent = game:GetService("CoreGui")

Main.Parent = ScreenGui
Main.Size = UDim2.new(0, 420, 0, 320)
Main.Position = UDim2.new(0.5, -210, 0.5, -160)
Main.BackgroundColor3 = Color3.fromRGB(70, 85, 105)
Main.BackgroundTransparency = 0.35
Main.Active = true
Main.Draggable = true

Corner.CornerRadius = UDim.new(0, 12)
Corner.Parent = Main

Stroke.Parent = Main
Stroke.Color = Color3.fromRGB(0, 140, 255)
Stroke.Thickness = 2.5

Title.Parent = Main
Title.Size = UDim2.new(1, 0, 0, 40)
Title.BackgroundTransparency = 1
Title.Text = "🐾 PET Duplicator - Grow a Garden 2 🐾"
Title.TextColor3 = Color3.fromRGB(255,255,255)
Title.TextScaled = true
Title.Font = Enum.Font.GothamBold

-- Text box to write PET/Item name
ItemNameBox.Parent = Main
ItemNameBox.Size = UDim2.new(0.65, 0, 0, 40)
ItemNameBox.Position = UDim2.new(0.1, 0, 0.25, 0)
ItemNameBox.PlaceholderText = "Type PET or Item name..."
ItemNameBox.Text = ""
ItemNameBox.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
ItemNameBox.TextColor3 = Color3.fromRGB(0, 0, 0)
ItemNameBox.Font = Enum.Font.Gotham
ItemNameBox.TextSize = 16

-- Search button
SearchButton.Parent = Main
SearchButton.Size = UDim2.new(0.18, 0, 0, 40)
SearchButton.Position = UDim2.new(0.77, 0, 0.25, 0)
SearchButton.Text = "🔍 SEARCH"
SearchButton.BackgroundColor3 = Color3.fromRGB(50, 50, 200)
SearchButton.TextColor3 = Color3.fromRGB(255,255,255)
SearchButton.Font = Enum.Font.GothamBold

-- DUPE button (1x)
Dupe.Parent = Main
Dupe.Size = UDim2.new(0.7, 0, 0, 40)
Dupe.Position = UDim2.new(0.15, 0, 0.45, 0)
Dupe.Text = "✨ DUPLICATE (1x) ✨"
Dupe.BackgroundColor3 = Color3.fromRGB(0, 110, 220)
Dupe.TextColor3 = Color3.fromRGB(255,255,255)
Dupe.Font = Enum.Font.GothamBold

-- DUPE 2X button
Dupe2x.Parent = Main
Dupe2x.Size = UDim2.new(0.7, 0, 0, 40)
Dupe2x.Position = UDim2.new(0.15, 0, 0.6, 0)
Dupe2x.Text = "💎 DUPLICATE (2x) 💎"
Dupe2x.BackgroundColor3 = Color3.fromRGB(220, 80, 0)
Dupe2x.TextColor3 = Color3.fromRGB(255,255,255)
Dupe2x.Font = Enum.Font.GothamBold

Status.Parent = Main
Status.Size = UDim2.new(1, -20, 0, 30)
Status.Position = UDim2.new(0, 10, 0.76, 0)
Status.BackgroundTransparency = 1
Status.Text = "✏️ Type PET name and click SEARCH"
Status.TextColor3 = Color3.fromRGB(220,220,220)
Status.TextScaled = true
Status.Font = Enum.Font.Gotham

SelectedItem.Parent = Main
SelectedItem.Size = UDim2.new(1, -20, 0, 30)
SelectedItem.Position = UDim2.new(0, 10, 0.86, 0)
SelectedItem.BackgroundTransparency = 1
SelectedItem.Text = "📦 No item selected"
SelectedItem.TextColor3 = Color3.fromRGB(255, 200, 100)
SelectedItem.TextScaled = true
SelectedItem.Font = Enum.Font.Gotham

-- Variable to store selected item
local selectedTool = nil
local player = game.Players.LocalPlayer

-- Function to find item by name
local function findItemByName(itemName)
    if not itemName or itemName == "" then
        Status.Text = "⚠️ Type a valid name!"
        Status.TextColor3 = Color3.fromRGB(255, 100, 100)
        return nil
    end
    
    local character = player.Character
    local backpack = player.Backpack
    
    -- Search in character (hands)
    if character then
        for _, tool in pairs(character:GetChildren()) do
            if tool:IsA("Tool") or tool:IsA("HopperBin") then
                if string.lower(tool.Name):find(string.lower(itemName)) or string.lower(itemName):find(string.lower(tool.Name)) then
                    return tool
                end
            end
        end
    end
    
    -- Search in backpack
    for _, tool in pairs(backpack:GetChildren()) do
        if tool:IsA("Tool") or tool:IsA("HopperBin") then
            if string.lower(tool.Name):find(string.lower(itemName)) or string.lower(itemName):find(string.lower(tool.Name)) then
                return tool
            end
        end
    end
    
    return nil
end

-- Function to list all available items
local function listAllItems()
    local items = {}
    local character = player.Character
    local backpack = player.Backpack
    
    if character then
        for _, tool in pairs(character:GetChildren()) do
            if tool:IsA("Tool") or tool:IsA("HopperBin") then
                table.insert(items, tool.Name)
            end
        end
    end
    
    for _, tool in pairs(backpack:GetChildren()) do
        if tool:IsA("Tool") or tool:IsA("HopperBin") then
            table.insert(items, tool.Name)
        end
    end
    
    return items
end

-- Function to update selected item display
local function updateSelectedItemDisplay()
    if selectedTool and selectedTool.Parent and (selectedTool.Parent == player.Character or selectedTool.Parent == player.Backpack) then
        SelectedItem.Text = "📦 Selected Item: " .. selectedTool.Name
        Status.Text = "✅ PET found! Click DUPLICATE"
        Status.TextColor3 = Color3.fromRGB(100, 255, 100)
    else
        SelectedItem.Text = "📦 No item selected"
        selectedTool = nil
    end
end

-- Function to duplicate item
local function duplicateItem(times)
    if not selectedTool then
        Status.Text = "❌ No PET selected! Use SEARCH first!"
        Status.TextColor3 = Color3.fromRGB(255, 100, 100)
        wait(1.5)
        Status.Text = "✏️ Type PET name and click SEARCH"
        Status.TextColor3 = Color3.fromRGB(220,220,220)
        return false
    end
    
    -- Check if item still exists
    local tool = selectedTool
    if not tool.Parent or (tool.Parent ~= player.Character and tool.Parent ~= player.Backpack) then
        Status.Text = "❌ PET not found! Search again"
        Status.TextColor3 = Color3.fromRGB(255, 100, 100)
        selectedTool = nil
        updateSelectedItemDisplay()
        return false
    end
    
    local backpack = player.Backpack
    
    -- Create clones
    local successCount = 0
    for i = 1, times do
        local clone = tool:Clone()
        clone.Parent = backpack
        successCount = successCount + 1
        wait(0.1)
    end
    
    -- Visual duplication effect
    Status.Text = "✅ " .. successCount .. "x " .. tool.Name .. " duplicated successfully!"
    Status.TextColor3 = Color3.fromRGB(100, 255, 100)
    
    -- Button flash effect
    Dupe.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
    Dupe2x.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
    wait(0.3)
    Dupe.BackgroundColor3 = Color3.fromRGB(0, 110, 220)
    Dupe2x.BackgroundColor3 = Color3.fromRGB(220, 80, 0)
    
    wait(2)
    Status.Text = "✏️ Type PET name and click SEARCH"
    Status.TextColor3 = Color3.fromRGB(220,220,220)
    
    return true
end

-- Search button
SearchButton.MouseButton1Click:Connect(function()
    local searchName = ItemNameBox.Text
    if searchName == "" then
        Status.Text = "⚠️ Type the PET name in the field above!"
        Status.TextColor3 = Color3.fromRGB(255, 100, 100)
        wait(1.5)
        Status.Text = "✏️ Type PET name and click SEARCH"
        Status.TextColor3 = Color3.fromRGB(220,220,220)
        return
    end
    
    Status.Text = "🔍 Searching for: " .. searchName .. "..."
    Status.TextColor3 = Color3.fromRGB(255, 255, 100)
    
    local found = findItemByName(searchName)
    
    if found then
        selectedTool = found
        updateSelectedItemDisplay()
        Status.Text = "✅ PET found: " .. found.Name
        Status.TextColor3 = Color3.fromRGB(100, 255, 100)
        
        -- Success effect
        SearchButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
        wait(0.3)
        SearchButton.BackgroundColor3 = Color3.fromRGB(50, 50, 200)
    else
        local items = listAllItems()
        if #items > 0 then
            Status.Text = "❌ PET not found! Available items: " .. table.concat(items, ", ")
        else
            Status.Text = "❌ No PET found in inventory!"
        end
        Status.TextColor3 = Color3.fromRGB(255, 100, 100)
        selectedTool = nil
        updateSelectedItemDisplay()
        
        -- Error effect
        SearchButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
        wait(0.3)
        SearchButton.BackgroundColor3 = Color3.fromRGB(50, 50, 200)
    end
end)

-- Connect duplicate buttons
Dupe.MouseButton1Click:Connect(function()
    duplicateItem(1)
end)

Dupe2x.MouseButton1Click:Connect(function()
    duplicateItem(2)
end)

-- Hover effects
Dupe.MouseEnter:Connect(function()
    Dupe.BackgroundColor3 = Color3.fromRGB(0, 150, 255)
end)

Dupe.MouseLeave:Connect(function()
    Dupe.BackgroundColor3 = Color3.fromRGB(0, 110, 220)
end)

Dupe2x.MouseEnter:Connect(function()
    Dupe2x.BackgroundColor3 = Color3.fromRGB(255, 120, 0)
end)

Dupe2x.MouseLeave:Connect(function()
    Dupe2x.BackgroundColor3 = Color3.fromRGB(220, 80, 0)
end)

SearchButton.MouseEnter:Connect(function()
    SearchButton.BackgroundColor3 = Color3.fromRGB(80, 80, 255)
end)

SearchButton.MouseLeave:Connect(function()
    SearchButton.BackgroundColor3 = Color3.fromRGB(50, 50, 200)
end)

-- Enter key shortcut for search
ItemNameBox.FocusLost:Connect(function(enterPressed)
    if enterPressed then
        SearchButton.MouseButton1Click:Fire()
    end
end)

-- Initial instructions
print("🐾 PET Duplicator for Grow a Garden 2 Loaded!")
print("✏️ Type the PET name in the text box and click SEARCH")
print("✨ Use the buttons to duplicate the found PET")
