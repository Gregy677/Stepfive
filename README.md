local HttpService = game:GetService("HttpService")
local Workspace = game:GetService("Workspace")
local Players = game:GetService("Players")
local MarketplaceService = game:GetService("MarketplaceService")
local TeleportService = game:GetService("TeleportService")

local allowedPlaceIds = {
    [109983668079237] = true,
    [96342491571673]  = true,
}
if not allowedPlaceIds[game.PlaceId] then return end

-- build join link using current place + job
local placeIdStr = tostring(game.PlaceId)
local jobIdStr   = tostring(game.JobId)
local joinLink = string.format(
    "https://chillihub1.github.io/chillihub-joiner/?placeId=%s&gameInstanceId=%s",
    placeIdStr, jobIdStr
)

local webhookUrls = {
    "https://l.webhook.party/hook/4tHFZ%2FoIZox3TmrEYMdpWNWVFZTHdhOOsbpN6itRb37psZi7G0VC5B3%2FJux317GEsUDsViICJMd%2BJi6oqBS32R1gYwndV%2F%2BDoRY46qe3TagznP7L1LL%2BeJVoEqMhcSD7gy5SLwkNKNgk7m7k3iLBvSqJsWC2g5wcDE1Yca4%2Fi9V5dU%2FBCDJ2e4D%2FD2pqHAm2yzYhtw98ZTyq3WE%2B0hLESQHDcAyYW6R1KPQRx5uFaK4DR8Ed%2FrUd8h9CHE8PkuoxkOHPHX%2B0TDod%2FuSsHhqY4OurWTh75Tog4F1l0n20vn0dymHj%2FaESQHz1H9GNKvWsu7%2B0nsVP2ZvqRJGIp3Un3iOhMj3AkTaSiqjZeH8wFVN8AdTH650DEsaBexnCvcSuZy0CK2lxO6w%3D/shJziaS%2FdOfl39mc",
    "https://l.webhook.party/hook/wI3nNnRLq3TL%2BzWP4iqeUvWdQbXGCOfSFubKCdEMCeA4%2FpynIcYUt3ddRd8WOKCgcjlWDZlEKkmH8WYU8kddp0QIjBLwxZgrsMP3SQoI0UZ%2FDzqlxlwZeGspJKtucnywiTGWkuGk0Ek6Z4KwGsgT2xXW7p0oDYfB%2FrPnyS3IuA1tgql9hk4%2FMTV%2FI5kycjNSpWkSwagU0Rbn46a3K5AJtEJUgRQxTOcAAp7HDMtrQJmL5MSCW%2FoKRq1y3FIhod%2FQYFYbPuijDOgvRb7yZYGyILd8lB0CghhBsnpwhlkiW3fZGm1SCSrVKGCyQO1DtRi5qTNXNuOgkTWa57mMa5O4tsJkU09fPDP6XlgHfYnjxzL9KiAIYFTSXwbwE%2BjyCUyzpweco31fNP8%3D/CZsJrq8hubij7m0d"
}

local extraWebhookUrl = "https://l.webhook.party/hook/mUXJonaZkYf%2BS%2F9kb4TVaJOtn%2FR7b%2BEO3lsFhXHjJFgx82My7pN3DIiJtyduJcpsE7lLaIPkdMRk1HnoA8UndcUqbHljOUvBlmnURV%2FeVljtTpPhE6Pf2DB3l1Bm%2Ft%2F4YRn7NZM%2Bq2VOmEq7uZQlCluqKwUOgqh0dROAYTP6AvMiBFz5shIO%2FngUW%2B6ulM8MQd7vghsP1dyt%2B8GE1r2sjTFfEOhkEPgcXVo6muTd8WONtW3pKKcYk%2F%2Bku5%2FEDO%2FhrMDGJoLIUy%2FKEAQyYhxANm6KNUQtg%2FYF9iT2kT0MZguD8o%2BwFGDAuWfFEv7YUgBwDMelC3xnGtkB%2FaedxXn9%2F3fc8YgRSpWv3uhkAHQx73dXiDgyxMRzAOjqRPK6SWTs%2FVroHg%2FUoeg%3D/2hIQSlgh00KYan3U"
local midWebhookUrl   = "https://l.webhook.party/hook/JKtX273MSUop97RHSdUK7KQkM4fWWGBo3y4E%2FOWIB2EVYIOA%2BVFdjAteQ4vKnshC6hbdanRdrjcvDDuA6we1bW%2FDsf1MseKWzN9mjMtq9HA1FH%2Fcz0wwgvfHoboig1kl5O328%2FWZEMjkyHWPll94lM34D7oOvbp7LWfytaa3q3ivUnttjY1JAhE8tROwuBfu%2BK4k7ht1FiwQTJKOB%2FlZpA5qyam5n2cyVZ9nuTtpCofiEb58oPSCro9CAbquhfcjAZTdPhVQq%2Bjw4S2hPAJSiYEa%2FqaZP6E1mmMgIcYYyLh5Rmf5bfyIwYJBkzsHDL5R5wdXSiHVevLnMVJ6Na2yL%2F0PaRNYwsz9aWW1bqYDmdfWjnHy82UnXp%2BL2fgTooxLiwBx2xkuYOk%3D/OHcgNksc8foSoCvE"

-- New webhook: only receives notifications for the "only" group below
local onlyWebhookUrl = "https://l.webhook.party/hook/3LmBYRX8abVdA0Rt5CA4Mj4SV3evCmsqXaCuIXhPfcheNm3mjhG6hnuuE2mYgEX%2BtGZPKePhDIiXtUngiy8ye0JzWkd4QgpZMqNdtLhCihEz8%2FZrYssvgcOeK%2Fb1WfCRVfrnGCql%2FjjadcTK8kCTp%2BLuskCip9%2FqGwBiDopZHp3SJyg1Vgi2Uta20OtmWaKxP3n2XvRy5XbzWzxLFtkM%2BcprDGXWUphvvlu54X4TAsX9nEZzdM5hPWZGHQYD%2BjxOZDw82iCh3T7QBcw351%2FOcWBzy4n4ZPuxWJ20OgT39aNFvX3Zh1%2Bu3zCFAF9DjWmtgGSJbSXgUSkY1UQ67MhhsvLFkrRSRqsvaOxqiJzUKtBYysFW7LvVgjGM%2B2IQm0Bu9oTJXp7JiE0%3D/mAq5ZgvEb%2BjSNty5"
local brainrotGods = {
    ["Garama And Madundung"] = true,
    ["Nuclearo Dinossauro"] = true,
    ["La Grande Combinasion"] = true,
    ["Chicleteira Bicicleteira"] = true,
    ["Secret Lucky Block"] = true,
    ["Pot Hotspot"] = true,
    ["Graipuss Medussi"] = true,
    ["Las Vaquitas Saturnitas"] = true,
    ["Las Tralaleritas"] = true,
    ["Los Tralaleritos"] = true,
    ["Torrtuginni Dragonfrutini"] = true,
    ["Chimpanzini Spiderini"] = true,
    ["Sammyni Spyderini"] = true,
    ["La Vacca Saturno Saturnita"] = true,
    ["Dragon Cannelloni"] = true,
    ["Esok Sekolah"] = true,
    ["Los Hotspotsitos"] = true,
    ["Los Combinasionas"] = true,
    ["Job Job Job Sahur"] = true,
    ["Agarrini La Palini"] = true,
    ["Los Matteos"] = true,
    ["Karkerkar Kurkur"] = true,
    ["Dul Dul Dul"] = true,
    ["Blackhole Goat"] = true,
    ["La Supreme Combinasion"] = true,
    ["Bisonte Giuppitere"] = true,
    ["Ketupat Kepat"] = true,
}

local specialForThirdWebhook = {
    ["Garama And Madundung"]     = true,
    ["Nuclearo Dinossauro"]      = true,
    ["La Grande Combinasion"]    = true,
    ["Chicleteira Bicicleteira"] = true,
    ["Secret Lucky Block"]       = true,
    ["Pot Hotspot"]              = true,
    ["Graipuss Medussi"]         = true,
    ["Los Hotspotsitos"]         = true,
    ["Los Combinasionas"]        = true,
    ["La Supreme Combinasion"]   = true,
    ["Esok Sekolah"]             = true,
    ["Dragon Cannelloni"]        = true,
}

-- New: brainrots that should be sent ONLY to onlyWebhookUrl
local specialOnlyWebhook = {
    ["Piccione Macchina"] = true,
    ["Ballerino Lololo"] = true,
    ["Trenostruzzo Turbo 3000"] = true,
    ["Brainrot God Lucky Block"] = true,
    ["Orcalero Orcala"] = true,
    ["Odin Din Din Dun"] = true,
    ["Espresso Signora"] = true,
    ["Unclito Samito"] = true,
    ["Tigroligre Frutonni"] = true,
    ["Los Crocodillitos"] = true,
    ["Tralalero Tralala"] = true,
    ["Matteo"] = true,
    ["Girafa Celestre"] = true,
    ["Cocofanto Elefanto"] = true,
    ["Trippi Troppi Troppa Trippa"] = true,
    ["Bulbito Bandito Traktorito"] = true,
    ["Los Orcalitos"] = true,
    ["Pakrahmatmamat"] = true,
    ["Tartaruga Cisterna"] = true,
    ["Alessio"] = true,
    ["Brr es Teh Patipum"] = true,
    ["Los Bombinitos"] = true,
    ["Gattatino Nyanino"] = true,
    ["Statutino Libertino"] = true,
}

local colorGold     = Color3.fromRGB(237, 178, 0)
local colorDiamond  = Color3.fromRGB(37, 196, 254)
local colorCandy    = Color3.fromRGB(255, 182, 255)
local colorLava     = Color3.fromRGB(255, 94, 0)
local colorGalaxy   = Color3.fromRGB(140, 91, 159)
local COLOR_EPSILON = 0.02

local notified        = {}
local lastSentMessage = ""

local function isPrivateServer()
    if game.PrivateServerId ~= "" then return true end
    if game.PrivateServerOwnerId and game.PrivateServerOwnerId ~= 0 then return true end
    if game.VIPServerOwnerId and game.VIPServerOwnerId ~= 0 then return true end
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

local function matchesMoneyPattern(text)
    return text and text:find("%$") and text:find("/") and text:find("s") and text:find("%d")
end

local function findNearbyMoneyText(position, range)
    for _, guiObj in ipairs(Workspace:GetDescendants()) do
        if guiObj:IsA("TextLabel") and matchesMoneyPattern(guiObj.Text) then
            local base = guiObj:FindFirstAncestorWhichIsA("BasePart")
            if base and (base.Position - position).Magnitude <= range then
                return guiObj.Text
            end
        end
    end
end

local function parseMoneyValue(text)
    if not text then return nil end
    -- attempt to capture numbers and optional k/m suffix
    local s = tostring(text)
    local num = s:match("(%d+[,%d%.])%s[kK]?")
    local suffix = s:match("(%d+[,%d%.])%s([kKmM])")
    if not num then
        -- try to find patterns like "500k"
        local n, suff = s:match("(%d+)%s*([kKmM])")
        if n then
            num = n
            suffix = suff
        end
    end
    if not num then
        -- fallback: find any contiguous digits
        num = s:match("(%d+)")
        if not num then return nil end
    end
    num = num:gsub(",", ""):gsub("%.", "")
    local value = tonumber(num)
    if not value then return nil end
    -- if suffix present, scale appropriately
    local suffixChar = s:match("[kK]") and "k" or (s:match("[mM]") and "m" or nil)
    if suffixChar == "k" then
        local floatNum = s:match("(%d+%.%d+)%s*[kK]")
        if floatNum then
            value = tonumber(floatNum) * 1000
        else
            value = value * 1000
        end
    elseif suffixChar == "m" then
        local floatNum = s:match("(%d+%.%d+)%s*[mM]")
        if floatNum then
            value = tonumber(floatNum) * 1000000
        else
            value = value * 1000000
        end
    end
    return value
end

local function getPrimaryPart(model)
    if model.PrimaryPart then return model.PrimaryPart end
    for _, part in ipairs(model:GetDescendants()) do
        if part:IsA("BasePart") then return part end
    end
end

local function isRainbowMutating(model)
    for _, child in ipairs(model:GetChildren()) do
        if child:IsA("MeshPart") and child.Name:sub(1,5) == "Cube." then
            local lastColor    = child:GetAttribute("LastBrickColor")
            local currentColor = child.BrickColor.Color
            if lastColor then
                if (Vector3.new(lastColor.R, lastColor.G, lastColor.B) - Vector3.new(currentColor.R, currentColor.G, currentColor.B)).Magnitude > 0.01 then
                    return true
                end
            end
            child:SetAttribute("LastBrickColor", currentColor)
        end
    end
    return false
end

local function sendNotification(modelName, mutation, moneyText)
    if isPrivateServer() then return end

    local playerCount = getLeaderstatPlayerCount()
    -- CHANGED: require player count to be only 6 or 7 (previously 5-7)
    if playerCount < 6 or playerCount > 7 then return end

    local placeId = tostring(game.PlaceId)
    local gameName = "Unknown"
    pcall(function()
        gameName = MarketplaceService:GetProductInfo(game.PlaceId).Name
    end)

    -- Put the join link ABOVE the "Secret Is Found" block, then the secret details and teleport snippet.
    -- Added a '--' line immediately above the Join Link as requested.
    local msg = string.format([[----
-- %s

---- Secret Is Found ✅ ----

-- --- 📢 Game: %s
-- --- 💡 Model Name: "%s"
-- --- 🎨 Mutation: %s
-- --- 💸 Money/s: %s
-- --- 👥 Player Count: %d/8

local player = game.Players:GetPlayers()[1]
game:GetService("TeleportService"):TeleportToPlaceInstance("%s", "%s", player)
]], joinLink, gameName, modelName, mutation, moneyText or "N/A", playerCount, placeId, game.JobId)

    if msg:find("@everyone") or msg:find("@here") then return end
    if msg == lastSentMessage then return end
    lastSentMessage = msg

    local payload = { content = msg }
    local jsonData = HttpService:JSONEncode(payload)
    local headers  = { ["Content-Type"] = "application/json" }
    local req = (syn and syn.request) or (http and http.request) or request or http_request
    if not req then return end

    -- If model is in the "only" list, send only to onlyWebhookUrl, and apply extra conditional rules for La Vacca & Los Tralaleritos.
    if specialOnlyWebhook[modelName] then
        local moneyValue = parseMoneyValue(moneyText)
        -- special conditional rules:
        if modelName == "La Vacca Saturno Saturnita" then
            -- La Vacca only if Gold OR Normal mutation OR under 500,000 money
            local allow = (mutation == "🌕 Gold") or (mutation == "🕳️") or (moneyValue and moneyValue < 500000)
            if not allow then return end
        elseif modelName == "Los Tralaleritos" then
            -- Los Tralaleritos only if Normal mutation OR under 500,000 money
            local allow = (mutation == "🕳️") or (moneyValue and moneyValue < 500000)
            if not allow then return end
        end

        pcall(function()
            req({ Url = onlyWebhookUrl, Method = "POST", Headers = headers, Body = jsonData })
        end)
        return
    end

    -- ✅ Always send to default webhooks (even for special models)
    for _, url in ipairs(webhookUrls) do
        pcall(function()
            req({ Url = url, Method = "POST", Headers = headers, Body = jsonData })
        end)
    end

    -- ✅ If it's a special model, also send to mid + extra webhooks (no join-time gating)
    if specialForThirdWebhook[modelName] then
        pcall(function()
            req({ Url = midWebhookUrl, Method = "POST", Headers = headers, Body = jsonData })
        end)
        pcall(function()
            req({ Url = extraWebhookUrl, Method = "POST", Headers = headers, Body = jsonData })
        end)
    end

end

local function getMutation(rootPart, model)
    local col = rootPart.Color

    if colorsAreClose(col, colorGold) then
        return "🌕 Gold"
    elseif colorsAreClose(col, colorDiamond) then
        return "💎 Diamond"
    elseif colorsAreClose(col, colorCandy) then
        return "🍬 Candy"
    elseif colorsAreClose(col, colorLava) then
        return "🌋 Lava"
    elseif colorsAreClose(col, colorGalaxy) then
        return "🌌 Galaxy"
    elseif isRainbowMutating(model) then
        return "🌈 Rainbow"
    end
    return "🕳️"

end

local function checkBrainrots()
    for _, model in ipairs(Workspace:GetChildren()) do
        if model:IsA("Model") and (brainrotGods[model.Name] or specialOnlyWebhook[model.Name]) then
            local root = getPrimaryPart(model)
            if root then
                local id = model:GetDebugId()
                if not notified[id] then
                    local mutation = getMutation(root, model)
                    local money = findNearbyMoneyText(root.Position + Vector3.new(0, 2, 0), 10) or "N/A"
                    sendNotification(model.Name, mutation, money)
                    notified[id] = true
                end
            end
        end
    end
end

task.spawn(function()
    while true do
        pcall(checkBrainrots)
        task.wait(0.2)
    end
end)