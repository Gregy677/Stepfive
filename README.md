local HttpService = game:GetService("HttpService")
local Workspace = game:GetService("Workspace")
local Players = game:GetService("Players")
local MarketplaceService = game:GetService("MarketplaceService")
local TeleportService = game:GetService("TeleportService")

-- Configuration
local allowedPlaceId = 109983668079237
if game.PlaceId ~= allowedPlaceId then return end

local webhookUrls = {
    "https://l.webhook.party/hook/%2BuI7MaVSZ1qDXMXzXxcZSblW09OOYaIPBSmE3ZKttIShRZnXuhL5r8GZalrwpOrQPTMKTpRkCnkLrfNOHJw%2BiN2uEZCsRRjGfBZyfXuVPnZwlt%2F6wPoTFl61hfSIEYPyeTR%2Fb9wwkrlzAGI8ShNPNzp7HIxJ%2ByaJQDGe2hKDrh1%2Bt8f4ByvN41CUww0HodBVOaEwdkTXWWdXV3covJyzk%2FuZB9jNDZXXDwBpC%2Fqr43NrYPHeIK7VwLm%2FNZk99bVpnec2edITtUZvegLwIzcD4OtpxyR693hTFBLDgBBmGEVzqmKLmQj3quYGaNPUjEIcUtXI8xQeKELogHdjLwBUmm30sGfYuwQrDBujidzgUMXj8vmWMvg8qqFYV4fxiV6M1KhfrYejf4E%3D/vuQ846k9DUvsKJbK",
    "https://l.webhook.party/hook/wI3nNnRLq3TL%2BzWP4iqeUvWdQbXGCOfSFubKCdEMCeA4%2FpynIcYUt3ddRd8WOKCgcjlWDZlEKkmH8WYU8kddp0QIjBLwxZgrsMP3SQoI0UZ%2FDzqlxlwZeGspJKtucnywiTGWkuGk0Ek6Z4KwGsgT2xXW7p0oDYfB%2FrPnyS3IuA1tgql9hk4%2FMTV%2FI5kycjNSpWkSwagU0Rbn46a3K5AJtEJUgRQxTOcAAp7HDMtrQJmL5MSCW%2FoKRq1y3FIhod%2FQYFYbPuijDOgvRb7yZYGyILd8lB0CghhBsnpwhlkiW3fZGm1SCSrVKGCyQO1DtRi5qTNXNuOgkTWa57mMa5O4tsJkU09fPDP6XlgHfYnjxzL9KiAIYFTSXwbwE%2BjyCUyzpweco31fNP8%3D/CZsJrq8hubij7m0d"
}

local extraWebhookUrl = "https://l.webhook.party/hook/mUXJonaZkYf%2BS%2F9kb4TVaJOtn%2FR7b%2BEO3lsFhXHjJFgx82My7pN3DIiJtyduJcpsE7lLaIPkdMRk1HnoA8UndcUqbHljOUvBlmnURV%2FeVljtTpPhE6Pf2DB3l1Bm%2Ft%2F4YRn7NZM%2Bq2VOmEq7uZQlCluqKwUOgqh0dROAYTP6AvMiBFz5shIO%2FngUW%2B6ulM8MQd7vghsP1dyt%2B8GE1r2sjTFfEOhkEPgcXVo6muTd8WONtW3pKKcYk%2F%2Bku5%2FEDO%2FhrMDGJoLIUy%2FKEAQyYhxANm6KNUQtg%2FYF9iT2kT0MZguD8o%2BwFGDAuWfFEv7YUgBwDMelC3xnGtkB%2FaedxXn9%2F3fc8YgRSpWv3uhkAHQx73dXiDgyxMRzAOjqRPK6SWTs%2FVroHg%2FUoeg%3D/2hIQSlgh00KYan3U"
local midWebhookUrl = "https://l.webhook.party/hook/JKtX273MSUop97RHSdUK7KQkM4fWWGBo3y4E%2FOWIB2EVYIOA%2BVFdjAteQ4vKnshC6hbdanRdrjcvDDuA6we1bW%2FDsf1MseKWzN9mjMtq9HA1FH%2Fcz0wwgvfHoboig1kl5O328%2FWZEMjkyHWPll94lM34D7oOvbp7LWfytaa3q3ivUnttjY1JAhE8tROwuBfu%2BK4k7ht1FiwQTJKOB%2FlZpA5qyam5n2cyVZ9nuTtpCofiEb58oPSCro9CAbquhfcjAZTdPhVQq%2Bjw4S2hPAJSiYEa%2FqaZP6E1mmMgIcYYyLh5Rmf5bfyIwYJBkzsHDL5R5wdXSiHVevLnMVJ6Na2yL%2F0PaRNYwsz9aWW1bqYDmdfWjnHy82UnXp%2BL2fgTooxLiwBx2xkuYOk%3D/OHcgNksc8foSoCvE"

local brainrotGods = {
    ["dragon cannelloni"] = true,
    ["garama and madundung"] = true,
    ["esok sekolah"] = true,
    ["los hotspotsitos"] = true,
    ["nuclearo dinossauro"] = true,
    ["los combinasionas"] = true,
    ["la grande combinasion"] = true,
    ["chicleteira bicicleteira"] = true,
    ["secret lucky block"] = true,
    ["pot hotspot"] = true,
    ["karkerkar kurkur"] = true,
    ["los tungtungtungcitos"] = true,
    ["graipuss medussi"] = true,
    ["las vaquitas saturnitas"] = true,
    ["las tralaleritas"] = true,
    ["los tralaleritos"] = true,
    ["agarrini la palini"] = true,
    ["torrtuginni dragonfrutini"] = true,
    ["chimpanzini spiderini"] = true,
    ["sammyini spidreini"] = true,
    ["la vacca saturno saturnita"] = true,
    ["los matteos"] = true,
    ["job job job sahur"] = true,
}

local specialForThirdWebhook = {
    ["dragon cannelloni"] = true,
    ["garama and madundung"] = true,
    ["esok sekolah"] = true,
    ["los hotspotsitos"] = true,
    ["nuclearo dinossauro"] = true,
    ["los combinasionas"] = true,
    ["pot hotspot"] = true,
    ["graipuss medussi"] = true,
    ["chicleteira bicicleteira"] = true,
    ["la grande combinasion"] = true,
    ["los matteos"] = true,
    ["job job job sahur"] = true,
}

local colorGold = Color3.fromRGB(237, 178, 0)
local colorDiamond = Color3.fromRGB(37, 196, 254)
local colorCandy = Color3.fromRGB(255, 70, 246)
local COLOR_EPSILON = 0.02

-- Tracking variables
local notifiedModels = {}
local playerJoinTimes = {}
local moneyLabels = {}
local lastMoneyScan = 0
local moneyScanCooldown = 5 -- seconds

-- Player tracking
Players.PlayerAdded:Connect(function(player)
    playerJoinTimes[player.UserId] = {
        joinTime = tick(),
        name = player.Name
    }
end)

Players.PlayerRemoving:Connect(function(player)
    playerJoinTimes[player.UserId] = nil
end)

-- Utility functions
local function isPrivateServer()
    if game.PrivateServerId ~= "" or (game.PrivateServerOwnerId and game.PrivateServerOwnerId ~= 0) or (game.VIPServerOwnerId and game.VIPServerOwnerId ~= 0) then 
        return true 
    end
    
    local players = Players:GetPlayers()
    if #players == 1 then
        local success, result = pcall(function() 
            return TeleportService:GetPlayerPlaceInstanceAsync(players[1].UserId) 
        end)
        
        if not success or not result or result.InstanceId ~= game.JobId then 
            return true 
        end
    end
    
    return false
end

local function getLeaderstatPlayerCount()
    local count = 0
    for _, player in ipairs(Players:GetPlayers()) do
        if player:FindFirstChild("leaderstats") then 
            count += 1 
        end
    end
    return count
end

local function colorsAreClose(a, b)
    return math.abs(a.R - b.R) < COLOR_EPSILON 
        and math.abs(a.G - b.G) < COLOR_EPSILON 
        and math.abs(a.B - b.B) < COLOR_EPSILON
end

-- Enhanced money detection
local function matchesMoneyPattern(text)
    if not text then return false end
    
    -- Patterns to match:
    -- $500k/s, $1M/s, $1b/s, $1,000/s, etc.
    local patterns = {
        "%$[%d,]+%.?%d*[kKmMbBtT]?/?s?",  -- $1M/s, $500k/s
        "%$[%d,]+%.?%d*%s*/%s*s",         -- $1,000 /s
        "%$[%d,]+%.?%d*%s*per%s*second",  -- $1,000 per second
        "[%d,]+%.?%d*%s*[kKmMbBtT]?%s*%$/?s?" -- 1M $/s
    }
    
    for _, pattern in ipairs(patterns) do
        if text:match(pattern) then
            return true
        end
    end
    
    return false
end

local function extractMoneyValue(text)
    if not text then return "N/A" end
    
    -- Find the numeric part with possible k/M/b suffixes
    local value = text:match("([%d,]+%.?%d*)([kKmMbBtT]?)")
    if not value then return "N/A" end
    
    local num, suffix = value:match("([%d,]+%.?%d*)([kKmMbBtT]?)")
    if not num then return "N/A" end
    
    -- Remove commas and convert to number
    num = tonumber((num:gsub(",", "")))
    if not num then return "N/A" end
    
    -- Apply suffix multiplier
    suffix = suffix:lower()
    if suffix == "k" then
        num = num * 1000
    elseif suffix == "m" then
        num = num * 1000000
    elseif suffix == "b" then
        num = num * 1000000000
    elseif suffix == "t" then
        num = num * 1000000000000
    end
    
    -- Format the number nicely
    if num >= 1000000000000 then
        return string.format("$%.1fT/s", num/1000000000000)
    elseif num >= 1000000000 then
        return string.format("$%.1fB/s", num/1000000000)
    elseif num >= 1000000 then
        return string.format("$%.1fM/s", num/1000000)
    elseif num >= 1000 then
        return string.format("$%.1fK/s", num/1000)
    else
        return string.format("$%d/s", num)
    end
end

local function scanForMoneyLabels()
    -- Only scan periodically to avoid performance issues
    if tick() - lastMoneyScan < moneyScanCooldown then return end
    lastMoneyScan = tick()
    
    moneyLabels = {} -- Clear old labels
    
    for _, obj in ipairs(Workspace:GetDescendants()) do
        if obj:IsA("TextLabel") or obj:IsA("TextButton") or obj:IsA("TextBox") then
            if matchesMoneyPattern(obj.Text) then
                local base = obj:FindFirstAncestorWhichIsA("BasePart")
                if base then
                    moneyLabels[base] = {
                        label = obj,
                        lastScan = tick(),
                        value = extractMoneyValue(obj.Text)
                    }
                end
            end
        end
    end
end

Workspace.DescendantAdded:Connect(function(obj)
    if obj:IsA("TextLabel") or obj:IsA("TextButton") or obj:IsA("TextBox") then
        if matchesMoneyPattern(obj.Text) then
            local base = obj:FindFirstAncestorWhichIsA("BasePart")
            if base then
                moneyLabels[base] = {
                    label = obj,
                    lastScan = tick(),
                    value = extractMoneyValue(obj.Text)
                }
            end
        end
    end
end)

local function getNearbyMoney(rootPart)
    if not rootPart then return "💸 N/A" end
    
    -- First try to find money labels attached to the model itself
    for _, descendant in ipairs(rootPart:GetDescendants()) do
        if (descendant:IsA("TextLabel") or descendant:IsA("TextButton") or descendant:IsA("TextBox")) and matchesMoneyPattern(descendant.Text) then
            return "💸 " .. extractMoneyValue(descendant.Text)
        end
    end
    
    -- Then scan nearby money labels in workspace
    scanForMoneyLabels()
    
    local closestMoney = nil
    local closestDistance = math.huge
    local currentTime = tick()
    
    for base, data in pairs(moneyLabels) do
        if base and base.Parent and data.label and data.label.Parent then
            -- Skip stale entries
            if currentTime - data.lastScan > moneyScanCooldown * 2 then
                moneyLabels[base] = nil
                continue
            end
            
            local dist = (base.Position - rootPart.Position).Magnitude
            if dist < 50 and dist < closestDistance then
                closestDistance = dist
                closestMoney = data.value
            end
        end
    end
    
    return closestMoney and "💸 " .. closestMoney or "💸 N/A"
end

local function getPrimaryPart(model)
    if model.PrimaryPart then return model.PrimaryPart end
    for _, part in ipairs(model:GetDescendants()) do
        if part:IsA("BasePart") then return part end
    end
    return nil
end

local function isRainbowMutating(model)
    for _, child in ipairs(model:GetChildren()) do
        if child:IsA("MeshPart") and child.Name:sub(1,5) == "Cube." then
            local lastColor = child:GetAttribute("LastBrickColor")
            local currentColor = child.BrickColor.Color
            
            if lastColor and (Vector3.new(lastColor.R, lastColor.G, lastColor.B) - Vector3.new(currentColor.R, currentColor.G, currentColor.B)).Magnitude > 0.01 then
                return true
            end
            
            child:SetAttribute("LastBrickColor", currentColor)
        end
    end
    return false
end

local function getPlayerList()
    local playerList = {}
    for _, player in ipairs(Players:GetPlayers()) do
        table.insert(playerList, player.Name)
    end
    return table.concat(playerList, ", ")
end

local function sendNotification(modelName, mutation, moneyText)
    if isPrivateServer() then return end
    local playerCount = getLeaderstatPlayerCount()
    if playerCount < 1 then return end

    -- Check if we've already notified about this model
    if notifiedModels[modelName] then return end
    notifiedModels[modelName] = true

    local placeId = tostring(game.PlaceId)
    local jobId = game.JobId
    local joinLink = string.format("https://chillihub1.github.io/chillihub-joiner/?placeId=%s&gameInstanceId=%s", placeId, jobId)
    local teleportCode = string.format("game:GetService('TeleportService'):TeleportToPlaceInstance(%s, '%s', game.Players.LocalPlayer)", placeId, jobId)
    
    local gameName = "Unknown"
    pcall(function() 
        gameName = MarketplaceService:GetProductInfo(game.PlaceId).Name 
    end)

    local playerList = getPlayerList()

    local embed = {
        ["embeds"] = {{
            ["title"] = "🤖 Secret Found by Ken Bot 🤖",
            ["description"] = string.format(
                "**Game:** %s\n**Model:** %s\n**Mutation:** %s\n**Money Rate:** %s\n**Players (%d/8):** %s",
                gameName, modelName, mutation, moneyText or "N/A", playerCount, playerList
            ),
            ["color"] = 0x00FF00, -- green
            ["fields"] = {
                {["name"] = "Join Link", ["value"] = joinLink, ["inline"] = true},
                {["name"] = "Teleport Code", ["value"] = "```lua\n" .. teleportCode .. "\n```", ["inline"] = true}
            },
            ["footer"] = {["text"] = "Private Project | " .. os.date("%X")}
        }}
    }

    local data = HttpService:JSONEncode(embed)
    local headers = { ["Content-Type"] = "application/json" }
    local requestFunc = (syn and syn.request) or (http and http.request) or request or http_request
    if not requestFunc then return end

    local lowerModel = modelName:lower()
    
    -- Send to appropriate webhooks based on model importance
    if lowerModel == "la grande combinasion" then
        for _, url in ipairs(webhookUrls) do
            pcall(function() requestFunc({ Url = url, Method = "POST", Headers = headers, Body = data }) end)
        end
        pcall(function() requestFunc({ Url = midWebhookUrl, Method = "POST", Headers = headers, Body = data }) end)
        pcall(function() requestFunc({ Url = extraWebhookUrl, Method = "POST", Headers = headers, Body = data }) end)
    elseif specialForThirdWebhook[lowerModel] then
        pcall(function() requestFunc({ Url = midWebhookUrl, Method = "POST", Headers = headers, Body = data }) end)
        pcall(function() requestFunc({ Url = extraWebhookUrl, Method = "POST", Headers = headers, Body = data }) end)
    else
        for _, url in ipairs(webhookUrls) do
            pcall(function() requestFunc({ Url = url, Method = "POST", Headers = headers, Body = data }) end)
        end
    end
end

local function checkBrainrots()
    for _, model in ipairs(Workspace:GetChildren()) do
        if model:IsA("Model") then
            local lowerName = model.Name:lower()
            if brainrotGods[lowerName] then
                local root = getPrimaryPart(model)
                if root then
                    local color = root.Color
                    local mutation = "🕳️"
                    
                    if colorsAreClose(color, colorGold) then 
                        mutation = "🌕 Gold"
                    elseif colorsAreClose(color, colorDiamond) then 
                        mutation = "💎 Diamond"
                    elseif colorsAreClose(color, colorCandy) then 
                        mutation = "🍬 Candy"
                    elseif isRainbowMutating(model) then 
                        mutation = "🌈 Rainbow" 
                    end

                    local money = getNearbyMoney(root)
                    sendNotification(model.Name, mutation, money)
                end
            end
        end
    end
end

-- Main loop
while true do
    pcall(checkBrainrots)
    task.wait(0.1)
end