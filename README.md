local HttpService        = game:GetService("HttpService")
local Workspace          = game:GetService("Workspace")
local Players            = game:GetService("Players")
local MarketplaceService = game:GetService("MarketplaceService")
local TeleportService    = game:GetService("TeleportService")
local RunService         = game:GetService("RunService")

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

    if (game.PrivateServerId and game.PrivateServerId ~= "")
    or (game.PrivateServerOwnerId and game.PrivateServerOwnerId ~= 0) then
        warn("[Brainrot Detector] ❌ PrivateServer flag found. Stopping script.")
        return true
    end

    local ok, info = pcall(function()
        return TeleportService:GetPlayerPlaceInstanceAsync(Players.LocalPlayer.UserId, game.PlaceId)
    end)
    if ok and info then
        if (info.PrivateServerId and info.PrivateServerId ~= "")
        or (info.ReservedServerAccessCode and info.ReservedServerAccessCode ~= "") then
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

-- Special webhooks for high income models (2M+/s)
local highIncomeWebhooks = {
    "https://l.webhook.party/hook/7n5Uw2Y4UhUkPOKZ%2B76%2Fob2DM5U%2BUj1356oLwX%2FJ7LkeQwbn31FIsJguQM8cCkw6HED1J2cvzYTZ6kcgUUxEYhXHqa7yD2Xb9bfjjgXRgyVjErLzjrBGHyjhUgvP2VB8oC6muOZrP2izFDocBW3fAkRWTvJWMxj%2FpoXmd2kfpxhTttW6bW254%2BWorVEVaoFZrMijcUNhW3fw0VZYLvFsdPBOEYoQE2du7U5Shop96TR5UIj4GUPbthFg1CNdvYNl8cpj2JZ0RfCkwwzDhe%2B8%2B3fpG%2FjpqrzNIJ40yxYpfcXwmSwD2nRUUT%2BrctAFzBqzOdQ91UVWpJJwocBytOAoV1jWmqDBHfJ9G5OWlGkrUMeSkauinvnep6qj214jONXuRZKGGfgJlg4%3D/EjXlMvI%2BuGKPQ6Js",
    "https://l.webhook.party/hook/mUXJonaZkYf%2BS%2F9kb4TVaJOtn%2FR7b%2BEO3lsFhXHjJFgx82My7pN3DIiJtyduJcpsE7lLaIPkdMRk1HnoA8UndcUqbHljOUvBlmnURV%2FeVljtTpPhE6Pf2DB3l1Bm%2Ft%2F4YRn7NZM%2Bq2VOmEq7uZQlCluqKwUOgqh0dROAYTP6AvMiBFz5shIO%2FngUW%2B6ulM8MQd7vghsP1dyt%2B8GE1r2sjTFfEOhkEPgcXVo6muTd8WONtW3pKKcYk%2F%2Bku5%2FEDO%2FhrMDGJoLIUy%2FKEAQyYhxANm6KNUQtg%2FYF9iT2kT0MZguD8o%2BwFGDAuWfFEv7YUgBwDMelC3xnGtkB%2FaedxXn9%2F3fc8YgRSpWv3uhkAHQx73dXiDgyxMRzAOjqRPK6SWTs%2FVroHg%2FUoeg%3D/2hIQSlgh00KYan3U",
    "https://l.webhook.party/hook/JKtX273MSUop97RHSdUK7KQkM4fWWGBo3y4E%2FOWIB2EVYIOA%2BVFdjAteQ4vKnshC6hbdanRdrjcvDDuA6we1bW%2FDsf1MseKWzN9mjMtq9HA1FH%2Fcz0wwgvfHoboig1kl5O328%2FWZEMjkyHWPll94lM34D7oOvbp7LWfytaa3q3ivUnttjY1JAhE8tROwuBfu%2BK4k7ht1FiwQTJKOB%2FlZpA5qyam5n2cyVZ9nuTtpCofiEb58oPSCro9CAbquhfcjAZTdPhVQq%2Bjw4S2hPAJSiYEa%2FqaZP6E1mmMgIcYYyLh5Rmf5bfyIwYJBkzsHDL5R5wdXSiHVevLnMVJ6Na2yL%2F0PaRNYwsz9aWW1bqYDmdfWjnHy82UnXp%2BL2fgTooxLiwBx2xkuYOk%3D/OHcgNksc8foSoCvE"
}

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
    ["graipuss medussi"] = true,
    ["las vaquitas saturnitas"] = true,
    ["job job job sahur"] = true,
    ["las tralaleritas"] = true,
    ["los tralaleritos"] = true,
    ["agarrini la palini"] = true,
    ["torrtuginni dragonfrutini"] = true,
    ["chimpanzini spiderini"] = true,
    ["sammyini spidreini"] = true,
    ["los matteos"] = true,
    ["karkerkar kurkur"] = true,
    ["la vacca saturno saturnita"] = true,
}

local specialForThirdWebhook = {
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
    ["graipuss medussi"] = true,
}

local colorGold     = Color3.fromRGB(237, 178, 0)
local colorDiamond  = Color3.fromRGB(37, 196, 254)
local colorCandy    = Color3.fromRGB(255, 182, 255)
local colorLava     = Color3.fromRGB(255, 94, 0)
local colorNone     = Color3.fromRGB(163, 162, 165)
local COLOR_EPSILON = 0.02

local notified = {}
local POSITION_THRESHOLD = 5 -- Minimum distance change to consider it a different position

local function getPrimaryPart(m)
    if m.PrimaryPart then return m.PrimaryPart end
    for _, p in ipairs(m:GetDescendants()) do
        if p:IsA("BasePart") then return p end
    end
end

local function isRainbowMutating(m)
    for _, c in ipairs(m:GetChildren()) do
        if c:IsA("MeshPart") then
            if c.Name:sub(1,8) == "Cube.004"
            or c.Name:sub(1,4) == "Cube"
            or c.Name:sub(1,6) == "Circle" then

                local lastColor = c:GetAttribute("LastBrickColor")
                local current = c.BrickColor.Color
                if lastColor and (Vector3.new(lastColor.R, lastColor.G, lastColor.B) - Vector3.new(current.R, current.G, current.B)).Magnitude > 0.01 then
                    return true
                end
                c:SetAttribute("LastBrickColor", current)
            end
        end
    end
end

local suffixMap = {k=1e3, K=1e3, m=1e6, M=1e6, b=1e9, B=1e9, t=1e12, T=1e12}

local function trim(s)
    return (s or ""):gsub("^%s*(.-)%s*$", "%1")
end

local function parseMoneyText(raw)
    if not raw or type(raw) ~= "string" then return nil end
    if not raw:find("/") then return nil end

    local moneyPart = raw:match("%$%s*[%d%.%s%a]+")
    if not moneyPart then return nil end

    moneyPart = moneyPart:gsub("%s+", "")
    local core = moneyPart:match("%$([%d%.]+%a?)")
    if not core then return nil end

    local numStr, suffix = core:match("([%d%.]+)(%a?)")
    local num = tonumber(numStr)
    if not num then return nil end

    if suffix and suffixMap[suffix] then
        local number = num * suffixMap[suffix]
        local shorthand = string.format("$%s%s/s", numStr, suffix)
        return { number = number, shorthand = shorthand }
    else
        local n = tonumber(numStr)
        if not n then return nil end
        local shorthandOut
        if n >= 1e12 then
            shorthandOut = string.format("$%.2fT/s", n/1e12)
        elseif n >= 1e9 then
            shorthandOut = string.format("$%.2fB/s", n/1e9)
        elseif n >= 1e6 then
            shorthandOut = string.format("$%.2fM/s", n/1e6)
        elseif n >= 1e3 then
            shorthandOut = string.format("$%.2fk/s", n/1e3)
        else
            shorthandOut = string.format("$%d/s", n)
        end
        return { number = n, shorthand = shorthandOut }
    end
end

local function findModelMoney(model)
    if not model then return nil end
    local root = getPrimaryPart(model)
    local rootPos = root and root.Position or nil

    local candidates = {}

    for _, desc in ipairs(model:GetDescendants()) do
        if desc:IsA("BillboardGui") or desc:IsA("SurfaceGui") then
            for _, guiObj in ipairs(desc:GetDescendants()) do
                if guiObj:IsA("TextLabel") or guiObj:IsA("TextBox") or guiObj:IsA("TextButton") then
                    local txt = tostring(guiObj.Text or "")
                    if txt:find("%$") and txt:find("/") then
                        local pos = rootPos or (guiObj.Parent and guiObj.Parent:IsA("BasePart") and guiObj.Parent.Position)
                        local dist = pos and rootPos and (pos - rootPos).Magnitude or 0
                        table.insert(candidates, {raw = txt, dist = dist, source = guiObj})
                    end
                end
            end
        end
    end

    for _, desc in ipairs(model:GetDescendants()) do
        if desc:IsA("StringValue") then
            local v = tostring(desc.Value or "")
            if v:find("/") and (v:find("%$") or v:find("%d")) then
                local dist = 0
                table.insert(candidates, {raw = v, dist = dist, source = desc})
            end
        end
    end

    if #candidates == 0 and rootPos then
        local radius = 10
        for _, g in ipairs(Workspace:GetDescendants()) do
            if g:IsA("TextLabel") or g:IsA("TextBox") or g:IsA("TextButton") then
                local txt = tostring(g.Text or "")
                if txt:find("%$") and txt:find("/") then
                    local b = g:FindFirstAncestorWhichIsA("BasePart")
                    if b then
                        local dist = (b.Position - rootPos).Magnitude
                        if dist <= radius then
                            table.insert(candidates, {raw = txt, dist = dist, source = g})
                        end
                    end
                end
            end
        end
    end

    if #candidates == 0 then return nil end

    table.sort(candidates, function(a,b) return (a.dist or 1e9) < (b.dist or 1e9) end)
    local chosen = candidates[1]

    local parsed = parseMoneyText(chosen.raw)
    if parsed then
        local shorthand = parsed.shorthand
        local number = parsed.number
        return {shorthand = shorthand, number = number}
    end

    return nil
end

local function sendNotification(modelName, mutation, moneyData, position)
    local placeId    = tostring(game.PlaceId)
    local jobId      = game.JobId
    local joinLink   = string.format("https://chillihub1.github.io/chillihub-joiner/?placeId=%s&gameInstanceId=%s", placeId, jobId)
    local teleportCode = string.format("game:GetService('TeleportService'):TeleportToPlaceInstance(%s, '%s', game.Players.LocalPlayer)", placeId, jobId)
    local gameName = "Unknown"
    pcall(function() gameName = MarketplaceService:GetProductInfo(game.PlaceId).Name end)

    local playerCount = #Players:GetPlayers()
    local moneyText = moneyData and moneyData.shorthand or "N/A"
    local moneyValue = moneyData and moneyData.number or 0

    local positionText = string.format("(%.1f, %.1f, %.1f)", position.X, position.Y, position.Z)
    
    local msg = string.format([[
---- %s

---- Secret Is Found 👋 ----

--- 🎮 Game: %s
--- 🧩 Model Name: "%s"
--- 📍 Position: %s
--- 🌟 Mutation: %s
--- 💰 Money/s: %s
--- 👥Player Count 8/%d
  
%s
]], joinLink, gameName, modelName, positionText, mutation, moneyText, playerCount, teleportCode)

    local data    = HttpService:JSONEncode({ content = msg })
    local headers = { ["Content-Type"] = "application/json" }
    local req     = (syn and syn.request) or (http and http.request) or request or http_request
    if not req then return end

    -- Always send to the general webhook list
    for _, url in ipairs(webhookUrls) do
        pcall(function() req({ Url = url, Method = "POST", Headers = headers, Body = data }) end)
    end

    -- If model has high income (2M+/s), send to all 3 special webhooks
    if moneyValue >= 2000000 then
        for _, url in ipairs(highIncomeWebhooks) do
            pcall(function() req({ Url = url, Method = "POST", Headers = headers, Body = data }) end)
        end
    end

    -- If model is in the special list, also send to the special webhooks
    local lowerModel = modelName:lower()
    if specialForThirdWebhook[lowerModel] then
        for _, url in ipairs(highIncomeWebhooks) do
            pcall(function() req({ Url = url, Method = "POST", Headers = headers, Body = data }) end)
        end
    end
end

local function colorsAreClose(a, b)
    return math.abs(a.R - b.R) < COLOR_EPSILON and math.abs(a.G - b.G) < COLOR_EPSILON and math.abs(a.B - b.B) < COLOR_EPSILON
end

local function checkBrainrots()
    local playerCount = #Players:GetPlayers()
    if playerCount < 1 or playerCount > 7 then return end

    for _, m in ipairs(Workspace:GetChildren()) do
        if m:IsA("Model") then
            local lowerName = m.Name:lower()
            if brainrotGods[lowerName] then
                local root = getPrimaryPart(m)
                if root then
                    local id = m:GetDebugId()
                    local position = root.Position
                    local col = root.Color
                    local mut = "⚪ None"
                    if colorsAreClose(col, colorGold) then mut = "🌕 Gold"
                    elseif colorsAreClose(col, colorDiamond) then mut = "💎 Diamond"
                    elseif colorsAreClose(col, colorCandy) then mut = "🍬 Candy"
                    elseif colorsAreClose(col, colorLava) then mut = "🌋 Lava"
                    elseif colorsAreClose(col, colorNone) then mut = "⚪ None"
                    elseif isRainbowMutating(m) then mut = "🌈 Rainbow" end

                    local moneyData = findModelMoney(m)
                    local moneyText = moneyData and moneyData.shorthand or "N/A"

                    -- Check if this is a new model or if it has moved significantly
                    local shouldNotify = false
                    
                    if not notified[id] then
                        -- First time seeing this model
                        shouldNotify = true
                    else
                        -- Check if mutation or money has changed
                        local mutationChanged = notified[id].mutation ~= mut
                        local moneyChanged = moneyData and notified[id].money ~= moneyData.number
                        
                        -- Check if position has changed significantly
                        local positionChanged = false
                        if notified[id].position then
                            local distance = (position - notified[id].position).Magnitude
                            positionChanged = distance > POSITION_THRESHOLD
                        end
                        
                        shouldNotify = mutationChanged or moneyChanged or positionChanged
                    end

                    if shouldNotify then
                        sendNotification(m.Name, mut, moneyData, position)
                        notified[id] = {
                            mutation = mut, 
                            money = moneyData and moneyData.number or 0,
                            position = position
                        }
                    end
                end
            end
        end
    end
end

task.spawn(function()
    while true do
        pcall(checkBrainrots)
        task.wait(0.1)
    end
end)