local HttpService = game:GetService("HttpService")
local Workspace = game:GetService("Workspace")
local Players = game:GetService("Players")
local MarketplaceService = game:GetService("MarketplaceService")
local TeleportService = game:GetService("TeleportService")
local RunService = game:GetService("RunService")

local allowedPlaceId = 109983668079237
if game.PlaceId ~= allowedPlaceId then return end

local function isNotPublicServer()
    if RunService:IsStudio() then
        warn("[Brainrot Detector] ❌ Studio detected. Stopping script.")
        return true
    end

    if not game.JobId or game.JobId == "" then
        warn("[Brainrot Detector] ❌ No JobId (not a public server). Stopping script.")
        return true
    end

    if (game.PrivateServerId and game.PrivateServerId ~= "") or (game.PrivateServerOwnerId and game.PrivateServerOwnerId ~= 0) then
        warn("[Brainrot Detector] ❌ PrivateServer flag found. Stopping script.")
        return true
    end

    local ok, info = pcall(function()
        return TeleportService:GetPlayerPlaceInstanceAsync(Players.LocalPlayer.UserId, game.PlaceId)
    end)
    if ok and info then
        if (info.PrivateServerId and info.PrivateServerId ~= "") or (info.ReservedServerAccessCode and info.ReservedServerAccessCode ~= "") then
            warn("[Brainrot Detector] ❌ Reserved/Private server detected via TeleportService. Stopping script.")
            return true
        end
    end

    if type(game.JobId) == "string" and #game.JobId < 16 then
        warn("[Brainrot Detector] ❌ Short JobId (suspicious). Stopping script.")
        return true
    end

    if #Players:GetPlayers() < 6 then
        warn("[Brainrot Detector] ❌ Low player count (" .. tostring(#Players:GetPlayers()) .. ") - likely private. Stopping script.")
        return true
    end

    return false
end

if isNotPublicServer() then
    return
else
    print("[Brainrot Detector] ✅ Public server detected. Script running...")
end

local webhookUrls = {
    "https://l.webhook.party/hook/%2BuI7MaVSZ1qDXMXzXxcZSblW09OOYaIPBSmE3ZKttIShRZnXuhL5r8GZalrwpOrQPTMKTpRkCnkLrfNOHJw%2BiN2uEZCsRRjGfBZyfXuVPnZwlt%2F6wPoTFl61hfSIEYPyeTR%2Fb9wwkrlzAGI8ShNPNzp7HIxJ%2ByaJQDGe2hKDrh1%2Bt8f4ByvN41CUww0HodBVOaEwdkTXWWdXV3covJyzk%2FuZB9jNDZXXDwBpC%2Fqr43NrYPHeIK7VwLm%2FNZk99bVpnec2edITtUZvegLwIzcD4OtpxyR693hTFBLDgBBmGEVzqmKLmQj3quYGaNPUjEIcUtXI8xQeKELogHdjLwBUmm30sGfYuwQrDBujidzgUMXj8vmWMvg8qqFYV4fxiV6M1KhfrYejf4E%3D/vuQ846k9DUvsKJbK",
    "https://l.webhook.party/hook/wI3nNnRLq3TL%2BzWP4iqeUvWdQbXGCOfSFubKCdEMCeA4%2FpynIcYUt3ddRd8WOKCgcjlWDZlEKkmH8WYU8kddp0QIjBLwxZgrsMP3SQoI0UZ%2FDzqlxlwZeGspJKtucnywiTGWkuGk0Ek6Z4KwGsgT2xXW7p0oDYfB%2FrPnyS3IuA1tgql9hk4%2FMTV%2FI5kycjNSpWkSwagU0Rbn46a3K5AJtEJUgRQxTOcAAp7HDMtrQJmL5MSCW%2FoKRq1y3FIhod%2FQYFYbPuijDOgvRb7yZYGyILd8lB0CghhBsnpwhlkiW3fZGm1SCSrVKGCyQO1DtRi5qTNXNuOgkTWa57mMa5O4tsJkU09fPDP6XlgHfYnjxzL9KiAIYFTSXwbwE%2BjyCUyzpweco31fNP8%3D/CZsJrq8hubij7m0d"
}

local specialWebhook = "https://l.webhook.party/hook/7n5Uw2Y4UhUkPOKZ%2B76%2Fob2DM5U%2BUj1356oLwX%2FJ7LkeQwbn31FIsJguQM8cCkw6HED1J2cvzYTZ6kcgUUxEYhXHqa7yD2Xb9bfjjgXRgyVjErLzjrBGHyjhUgvP2VB8oC6muOZrP2izFDocBW3fAkRWTvJWMxj%2FpoXmd2kfpxhTttW6bW254%2BWorVEVaoFZrMijcUNhW3fw0VZYLvFsdPBOEYoQE2du7U5Shop96TR5UIj4GUPbthFg1CNdvYNl8cpj2JZ0RfCkwwzDhe%2B8%2B3fpG%2FjpqrzNIJ40yxYpfcXwmSwD2nRUUT%2BrctAFzBqzOdQ91UVWpJJwocBytOAoV1jWmqDBHfJ9G5OWlGkrUMeSkauinvnep6qj214jONXuRZKGGfgJlg4%3D/EjXlMvI%2BuGKPQ6Js"

local colorGold = Color3.fromRGB(237, 178, 0)
local colorDiamond = Color3.fromRGB(37, 196, 254)
local colorCandy = Color3.fromRGB(255, 182, 255)
local colorLava = Color3.fromRGB(255, 94, 0)
local colorNone = Color3.fromRGB(163, 162, 165)
local COLOR_EPSILON = 0.02

local notified = {}
local moneyCache = {}

local function getPrimaryPart(m)
    if m.PrimaryPart then return m.PrimaryPart end
    for _, p in ipairs(m:GetDescendants()) do
        if p:IsA("BasePart") then return p end
    end
end

local function isRainbowMutating(m)
    for _, c in ipairs(m:GetChildren()) do
        if c:IsA("MeshPart") then
            if c.Name:sub(1,8) == "Cube.004" or c.Name:sub(1,4) == "Cube" or c.Name:sub(1,6) == "Circle" then
                local lastColor = c:GetAttribute("LastBrickColor")
                local current = c.BrickColor.Color
                if lastColor and (Vector3.new(lastColor.R, lastColor.G, lastColor.B) - Vector3.new(current.R, current.G, current.B)).Magnitude > 0.01 then
                    return true
                end
                c:SetAttribute("LastBrickColor", current)
            end
        end
    end
    return false
end

local suffixMap = {k=1e3, K=1e3, m=1e6, M=1e6, b=1e9, B=1e9, t=1e12, T=1e12}

local function parseMoneyText(raw)
    if not raw or type(raw) ~= "string" then return nil end
    
    -- Clean the text
    local cleanText = raw:gsub(",", ""):gsub(" ", "")
    
    -- Try to find money patterns
    local patterns = {
        "%$([%d%.]+)([kKmMbBtT]?)/s",
        "%$([%d%.]+)([kKmMbBtT]?)%/s",
        "%$([%d%.]+)([kKmMbBtT]?) per second",
        "([%d%.]+)([kKmMbBtT]?)/s",
        "([%d%.]+)([kKmMbBtT]?)%/s"
    }
    
    for _, pattern in ipairs(patterns) do
        local numStr, suffix = cleanText:match(pattern)
        if numStr then
            local num = tonumber(numStr)
            if num then
                if suffix and suffix ~= "" and suffixMap[suffix] then
                    num = num * suffixMap[suffix]
                end
                
                -- Format the shorthand
                local shorthand
                if num >= 1e12 then
                    shorthand = string.format("$%.2fT/s", num/1e12)
                elseif num >= 1e9 then
                    shorthand = string.format("$%.2fB/s", num/1e9)
                elseif num >= 1e6 then
                    shorthand = string.format("$%.2fM/s", num/1e6)
                elseif num >= 1e3 then
                    shorthand = string.format("$%.2fk/s", num/1e3)
                else
                    shorthand = string.format("$%d/s", num)
                end
                
                return {number = num, shorthand = shorthand}
            end
        end
    end
    
    return nil
end

local function findModelMoney(model)
    if not model then return nil end
    
    -- Check cache first
    local modelId = model:GetDebugId()
    if moneyCache[modelId] then
        return moneyCache[modelId].shorthand, moneyCache[modelId].number
    end
    
    local root = getPrimaryPart(model)
    local rootPos = root and root.Position or nil

    local bestMoney = nil
    local bestDistance = math.huge

    -- Search for money text in the model's descendants
    for _, desc in ipairs(model:GetDescendants()) do
        if desc:IsA("BillboardGui") or desc:IsA("SurfaceGui") then
            for _, guiObj in ipairs(desc:GetDescendants()) do
                if guiObj:IsA("TextLabel") or guiObj:IsA("TextBox") or guiObj:IsA("TextButton") then
                    local txt = tostring(guiObj.Text or "")
                    local parsed = parseMoneyText(txt)
                    
                    if parsed then
                        local part = desc:FindFirstAncestorOfClass("BasePart")
                        local distance = part and rootPos and (part.Position - rootPos).Magnitude or 0
                        
                        if distance < bestDistance then
                            bestMoney = parsed
                            bestDistance = distance
                        end
                    end
                end
            end
        elseif desc:IsA("StringValue") then
            local txt = tostring(desc.Value or "")
            local parsed = parseMoneyText(txt)
            
            if parsed then
                bestMoney = parsed
                bestDistance = 0  -- StringValue is part of the model, so distance is 0
            end
        end
    end

    -- If no money found in model, search nearby
    if not bestMoney and rootPos then
        local radius = 7.5
        for _, obj in ipairs(Workspace:GetDescendants()) do
            if (obj:IsA("TextLabel") or obj:IsA("TextBox") or obj:IsA("TextButton")) and obj.Text then
                local txt = tostring(obj.Text)
                local parsed = parseMoneyText(txt)
                
                if parsed then
                    local part = obj:FindFirstAncestorOfClass("BasePart")
                    if part then
                        local distance = (part.Position - rootPos).Magnitude
                        if distance <= radius and distance < bestDistance then
                            bestMoney = parsed
                            bestDistance = distance
                        end
                    end
                end
            end
        end
    end

    -- Cache the result
    if bestMoney then
        moneyCache[modelId] = bestMoney
        return bestMoney.shorthand, bestMoney.number
    end
    
    return nil, 0
end

local function sendNotification(modelName, mutation, moneyText, moneyValue, isSpecial)
    local placeId = tostring(game.PlaceId)
    local jobId = game.JobId
    local joinLink = string.format("https://chillihub1.github.io/chillihub-joiner/?placeId=%s&gameInstanceId=%s", placeId, jobId)
    local teleportCode = string.format("game:GetService('TeleportService'):TeleportToPlaceInstance(%s, '%s', game.Players.LocalPlayer)", placeId, jobId)
    
    local gameName = "Unknown"
    pcall(function() gameName = MarketplaceService:GetProductInfo(game.PlaceId).Name end)

    local playerCount = #Players:GetPlayers()

    local msg = string.format([[
---- %s

---- %s Found 👋 ----

--- 🎮 Game: %s
--- 🧩 Model Name: "%s"
--- 🌟 Mutation: %s
--- 💰 Money/s: %s
--- 👥 Player Count: %d/%d
  
%s
]], joinLink, isSpecial and "HIGH VALUE" or "Secret", gameName, modelName, mutation, moneyText or "N/A", playerCount, 8, teleportCode)

    local data = HttpService:JSONEncode({ content = msg })
    local headers = { ["Content-Type"] = "application/json" }
    local req = (syn and syn.request) or (http and http.request) or request or http_request
    if not req then return end

    -- Send to appropriate webhooks
    if isSpecial then
        -- High value models go to special webhook only
        pcall(function() req({ Url = specialWebhook, Method = "POST", Headers = headers, Body = data }) end)
    else
        -- Regular models go to general webhooks
        for _, url in ipairs(webhookUrls) do
            pcall(function() req({ Url = url, Method = "POST", Headers = headers, Body = data }) end)
        end
    end
end

local function colorsAreClose(a, b)
    return math.abs(a.R - b.R) < COLOR_EPSILON and math.abs(a.G - b.G) < COLOR_EPSILON and math.abs(a.B - b.B) < COLOR_EPSILON
end

local function getMutationColor(root)
    if colorsAreClose(root.Color, colorGold) then return "🌕 Gold"
    elseif colorsAreClose(root.Color, colorDiamond) then return "💎 Diamond"
    elseif colorsAreClose(root.Color, colorCandy) then return "🍬 Candy"
    elseif colorsAreClose(root.Color, colorLava) then return "🌋 Lava"
    elseif colorsAreClose(root.Color, colorNone) then return "⚪ None"
    else return "⚪ Unknown" end
end

local function checkAllModels()
    local playerCount = #Players:GetPlayers()
    if playerCount < 1 or playerCount > 7 then return end

    for _, model in ipairs(Workspace:GetChildren()) do
        if model:IsA("Model") then
            local root = getPrimaryPart(model)
            if root then
                local modelId = model:GetDebugId()
                
                -- Get mutation type
                local mutation = getMutationColor(root)
                if isRainbowMutating(model) then
                    mutation = "🌈 Rainbow"
                end
                
                -- Get money value
                local moneyText, moneyValue = findModelMoney(model)
                
                -- Check if we should notify
                local shouldNotify = false
                local isSpecial = false
                
                if moneyValue and moneyValue >= 1000000 then
                    -- High value model (over 1M/s)
                    shouldNotify = true
                    isSpecial = true
                elseif not notified[modelId] then
                    -- New model we haven't seen before
                    shouldNotify = true
                elseif notified[modelId].mutation ~= mutation or notified[modelId].moneyValue ~= moneyValue then
                    -- Model has changed
                    shouldNotify = true
                    isSpecial = moneyValue and moneyValue >= 1000000
                end
                
                if shouldNotify then
                    sendNotification(model.Name, mutation, moneyText, moneyValue, isSpecial)
                    notified[modelId] = {
                        mutation = mutation,
                        moneyText = moneyText,
                        moneyValue = moneyValue,
                        timestamp = os.time()
                    }
                end
            end
        end
    end
    
    -- Clean up cache and notifications for removed models
    for id, info in pairs(notified) do
        if os.time() - info.timestamp > 300 then -- 5 minutes
            notified[id] = nil
        end
    end
    
    for id, info in pairs(moneyCache) do
        if os.time() - (info.timestamp or 0) > 60 then -- 1 minute
            moneyCache[id] = nil
        end
    end
end

-- Run the scanner
task.spawn(function()
    while true do
        pcall(checkAllModels)
        task.wait(0.5) -- Slightly longer interval to reduce performance impact
    end
end)