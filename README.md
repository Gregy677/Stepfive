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

-- Money detection configuration
local moneyScanCooldown = 2 -- seconds between scans
local detectionRange = 50 -- studs
local lastMoneyScan = 0
local moneyLabels = {}
local detectedHighValues = {} -- Track already detected high values

-- Words to ignore in money labels
local ignoreWords = {
    "cash", "offline", "font", "color", "rgb", "hex", "profit", "income", 
    "total", "balance", "wallet", "bank", "collected", "earned", "gained"
}

-- Money pattern detection
local function matchesMoneyPattern(text)
    if not text or type(text) ~= "string" then return false end
    
    -- Check for ignored words
    local lowerText = text:lower()
    for _, word in ipairs(ignoreWords) do
        if lowerText:find(word) then
            return false
        end
    end
    
    -- Patterns to match money formats (only per-second values)
    local patterns = {
        "%$[%d,]+%.?%d*[kKmMbBtT]/s",             -- $1M/s, $500k/s
        "%$[%d,]+%.?%d*%s*/%s*s",                 -- $1,000 /s
        "%$[%d,]+%.?%d*%s*per%s*second",          -- $1,000 per second
        "[%d,]+%.?%d*%s*[kKmMbBtT]%s*%$/s",       -- 1M $/s
        "[%d,]+%.?%d*%s*[kKmMbBtT]%s*%/s",        -- 1M/s
        "%+%s*[%d,]+%.?%d*%s*[kKmMbBtT]%s*%$/s",  -- + 1M $/s
    }
    
    for _, pattern in ipairs(patterns) do
        if text:match(pattern) then
            return true
        end
    end
    
    return false
end

-- Extract and format money value
local function extractMoneyValue(text)
    if not text then return 0, "N/A" end
    
    -- Find the numeric part with possible k/M/b suffixes
    local numPart, suffix = text:match("([%d,]+%.?%d*)([kKmMbBtT])")
    if not numPart then return 0, "N/A" end
    
    -- Remove commas and convert to number
    local num = tonumber((numPart:gsub(",", "")))
    if not num then return 0, "N/A" end
    
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
    local formattedValue
    if num >= 1000000000000 then
        formattedValue = string.format("$%.1fT/s", num/1000000000000)
    elseif num >= 1000000000 then
        formattedValue = string.format("$%.1fB/s", num/1000000000)
    elseif num >= 1000000 then
        formattedValue = string.format("$%.1fM/s", num/1000000)
    elseif num >= 1000 then
        formattedValue = string.format("$%.1fK/s", num/1000)
    else
        formattedValue = string.format("$%d/s", num)
    end
    
    return num, formattedValue
end

-- Find the model that contains the money text
local function findModelForMoneyText(moneyObject)
    if not moneyObject then return "Unknown" end
    
    -- Try to find the parent model
    local current = moneyObject
    while current and current ~= Workspace do
        if current:IsA("Model") then
            return current.Name
        end
        current = current.Parent
    end
    
    -- If no model found, look for the closest model to the text position
    local textPosition
    if moneyObject:IsA("BasePart") then
        textPosition = moneyObject.Position
    elseif moneyObject:IsA("GuiObject") then
        textPosition = moneyObject.AbsolutePosition
    else
        return "Unknown"
    end
    
    local closestModel = nil
    local closestDistance = math.huge
    
    for _, model in ipairs(Workspace:GetChildren()) do
        if model:IsA("Model") then
            local primaryPart = model.PrimaryPart or model:FindFirstChildWhichIsA("BasePart")
            if primaryPart then
                local distance = (primaryPart.Position - textPosition).Magnitude
                if distance < 20 and distance < closestDistance then
                    closestDistance = distance
                    closestModel = model
                end
            end
        end
    end
    
    return closestModel and closestModel.Name or "Unknown"
end

-- Scan for money labels in the workspace
local function scanForMoneyLabels()
    if tick() - lastMoneyScan < moneyScanCooldown then return end
    lastMoneyScan = tick()
    
    -- Clear old labels that might no longer exist
    for base, data in pairs(moneyLabels) do
        if not base or not base.Parent then
            moneyLabels[base] = nil
        end
    end
    
    -- Scan all descendants for money labels
    for _, obj in ipairs(Workspace:GetDescendants()) do
        if (obj:IsA("TextLabel") or obj:IsA("TextButton") or obj:IsA("TextBox")) and matchesMoneyPattern(obj.Text) then
            local base = obj:FindFirstAncestorWhichIsA("BasePart") or obj
            local numericValue, formattedValue = extractMoneyValue(obj.Text)
            
            -- Only track values over 1M/s
            if numericValue >= 1000000 then
                moneyLabels[base] = {
                    label = obj,
                    lastScan = tick(),
                    value = formattedValue,
                    numericValue = numericValue,
                    position = base:IsA("BasePart") and base.Position or base.AbsolutePosition,
                    rawText = obj.Text,
                    modelName = findModelForMoneyText(obj)
                }
            end
        end
    end
end

-- Find the closest money label to a position
local function findClosestMoney(position)
    local closestMoney = nil
    local closestDistance = math.huge
    local currentTime = tick()
    
    for base, data in pairs(moneyLabels) do
        if base and base.Parent and data.label and data.label.Parent then
            -- Skip stale entries
            if currentTime - data.lastScan > moneyScanCooldown * 3 then
                moneyLabels[base] = nil
                continue
            end
            
            local dist = (data.position - position).Magnitude
            if dist < detectionRange and dist < closestDistance then
                closestDistance = dist
                closestMoney = data
                closestMoney.basePart = base
            end
        end
    end
    
    return closestMoney, closestDistance
end

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

local function getPlayerList()
    local playerList = {}
    for _, player in ipairs(Players:GetPlayers()) do
        table.insert(playerList, player.Name)
    end
    return table.concat(playerList, ", ")
end

-- Send notification to webhook
local function sendMoneyNotification(moneyValue, distance, position, modelName, rawText)
    if isPrivateServer() then return end
    
    -- Check if we've already notified about this value
    local valueKey = moneyValue .. tostring(math.floor(position.X)) .. tostring(math.floor(position.Y)) .. tostring(math.floor(position.Z))
    if detectedHighValues[valueKey] then return end
    detectedHighValues[valueKey] = true
    
    local placeId = tostring(game.PlaceId)
    local jobId = game.JobId
    local joinLink = string.format("https://chillihub1.github.io/chillihub-joiner/?placeId=%s&gameInstanceId=%s", placeId, jobId)
    local teleportCode = string.format("game:GetService('TeleportService'):TeleportToPlaceInstance(%s, '%s', game.Players.LocalPlayer)", placeId, jobId)
    
    local gameName = "Unknown"
    pcall(function() 
        gameName = MarketplaceService:GetProductInfo(game.PlaceId).Name 
    end)

    local playerList = getPlayerList()
    local playerCount = #Players:GetPlayers()

    local embed = {
        ["embeds"] = {{
            ["title"] = "💰 High Money Value Detected 💰",
            ["description"] = string.format(
                "**Game:** %s\n**Money Value:** %s\n**Model Name:** %s\n**Distance:** %.1f studs\n**Position:** (%.1f, %.1f, %.1f)\n**Players (%d):** %s\n**Raw Text:** %s",
                gameName, moneyValue, modelName or "Unknown", distance, position.X, position.Y, position.Z, playerCount, playerList, rawText or "N/A"
            ),
            ["color"] = 0xFFD700, -- gold color
            ["fields"] = {
                {["name"] = "Join Link", ["value"] = joinLink, ["inline"] = true},
                {["name"] = "Teleport Code", ["value"] = "```lua\n" .. teleportCode .. "\n```", ["inline"] = true}
            },
            ["footer"] = {["text"] = "Money Scanner | " .. os.date("%X")},
            ["timestamp"] = os.date("!%Y-%m-%dT%H:%M:%SZ")
        }}
    }

    local data = HttpService:JSONEncode(embed)
    local headers = { ["Content-Type"] = "application/json" }
    local requestFunc = (syn and syn.request) or (http and http.request) or request or http_request
    if not requestFunc then return end

    -- Send to all webhooks
    for _, url in ipairs(webhookUrls) do
        pcall(function() requestFunc({ Url = url, Method = "POST", Headers = headers, Body = data }) end)
    end
    pcall(function() requestFunc({ Url = midWebhookUrl, Method = "POST", Headers = headers, Body = data }) end)
    pcall(function() requestFunc({ Url = extraWebhookUrl, Method = "POST", Headers = headers, Body = data }) end)
end

-- Main detection loop
local function monitorHighMoneyValues()
    while true do
        pcall(function()
            -- Scan for money labels
            scanForMoneyLabels()
            
            -- Check for each player
            for _, player in ipairs(Players:GetPlayers()) do
                if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                    local rootPart = player.Character.HumanoidRootPart
                    local closestMoney, distance = findClosestMoney(rootPart.Position)
                    
                    if closestMoney then
                        sendMoneyNotification(
                            closestMoney.value, 
                            distance, 
                            closestMoney.position, 
                            closestMoney.modelName, 
                            closestMoney.rawText
                        )
                    end
                end
            end
            
            -- Clean up old detected values (after 5 minutes)
            for key, timestamp in pairs(detectedHighValues) do
                if tick() - timestamp > 300 then
                    detectedHighValues[key] = nil
                end
            end
        end)
        
        task.wait(moneyScanCooldown)
    end
end

-- Start monitoring
coroutine.wrap(monitorHighMoneyValues)()