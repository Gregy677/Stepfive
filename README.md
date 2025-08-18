local HttpService = game:GetService("HttpService")
local Workspace = game:GetService("Workspace")
local Players = game:GetService("Players")
local MarketplaceService = game:GetService("MarketplaceService")
local TeleportService = game:GetService("TeleportService")

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

local notified = {}
local lastSentMessage = ""
local playerJoinTimes = {}
local foundBrainrots = {}
local finders = {} -- Table to track who found each brainrot

Players.PlayerAdded:Connect(function(player) playerJoinTimes[player.UserId] = tick() end)
Players.PlayerRemoving:Connect(function(player) playerJoinTimes[player.UserId] = nil end)

local function isPrivateServer()
    if game.PrivateServerId ~= "" or (game.PrivateServerOwnerId and game.PrivateServerOwnerId ~= 0) or (game.VIPServerOwnerId and game.VIPServerOwnerId ~= 0) then 
        return true 
    end
    local pl = Players:GetPlayers()
    if #pl == 1 then
        local s, r = pcall(function() return TeleportService:GetPlayerPlaceInstanceAsync(pl[1].UserId) end)
        if not s or not r or r.InstanceId ~= game.JobId then return true end
    end
    return false
end

local function getLeaderstatPlayerCount()
    local c = 0
    for _, p in ipairs(Players:GetPlayers()) do
        if p:FindFirstChild("leaderstats") then c += 1 end
    end
    return c
end

local function colorsAreClose(a, b)
    return math.abs(a.R - b.R) < COLOR_EPSILON and math.abs(a.G - b.G) < COLOR_EPSILON and math.abs(a.B - b.B) < COLOR_EPSILON
end

local function getPrimaryPart(m)
    if m.PrimaryPart then return m.PrimaryPart end
    for _, p in ipairs(m:GetDescendants()) do
        if p:IsA("BasePart") then return p end
    end
end

local function isRainbowMutating(m)
    for _, c in ipairs(m:GetChildren()) do
        if c:IsA("MeshPart") and c.Name:sub(1,5) == "Cube." then
            local l = c:GetAttribute("LastBrickColor")
            local cu = c.BrickColor.Color
            if l and (Vector3.new(l.R, l.G, l.B) - Vector3.new(cu.R, cu.G, cu.B)).Magnitude > 0.01 then
                return true
            end
            c:SetAttribute("LastBrickColor", cu)
        end
    end
end

local function getClosestPlayer(position)
    local closestPlayer = nil
    local closestDistance = math.huge
    
    for _, player in ipairs(Players:GetPlayers()) do
        local character = player.Character
        if character then
            local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
            if humanoidRootPart then
                local distance = (humanoidRootPart.Position - position).Magnitude
                if distance < closestDistance then
                    closestDistance = distance
                    closestPlayer = player
                end
            end
        end
    end
    
    return closestPlayer
end

local function sendNotification(brainrotList)
    if isPrivateServer() then return end
    local playerCount = getLeaderstatPlayerCount()
    if playerCount < 1 then return end

    local placeId = tostring(game.PlaceId)
    local jobId = game.JobId
    local joinLink = string.format("https://www.roblox.com/games/%s?jobId=%s", placeId, jobId)
    local teleportCode = string.format("game:GetService('TeleportService'):TeleportToPlaceInstance(%s, '%s', game.Players.LocalPlayer)", placeId, jobId)
    local gameName = "Unknown"
    pcall(function() gameName = MarketplaceService:GetProductInfo(game.PlaceId).Name end)

    -- Format the brainrot list with finders
    local brainrotText = ""
    for name, data in pairs(brainrotList) do
        local finderName = "Unknown"
        if data.finder then
            finderName = data.finder.Name
        end
        brainrotText = brainrotText .. string.format("%s x%d (Found by: %s)\n", name, data.count, finderName)
    end

    local embed = {
        ["title"] = "📌 Brainrot Finder Alert",
        ["description"] = "Multiple brainrots detected in this server! Jump fast before they're gone.",
        ["color"] = 65280, -- Green color
        ["fields"] = {
            {
                ["name"] = "📌 User",
                ["value"] = "AJANAUTOFARM",
                ["inline"] = true
            },
            {
                ["name"] = "📌 Player Count",
                ["value"] = tostring(playerCount),
                ["inline"] = true
            },
            {
                ["name"] = "📋 Brainrot Log",
                ["value"] = brainrotText,
                ["inline"] = false
            },
            {
                ["name"] = "📌 Server Link",
                ["value"] = joinLink,
                ["inline"] = false
            },
            {
                ["name"] = "📌 Join Script",
                ["value"] = "game:GetService('TeleportService')",
                ["inline"] = false
            }
        },
        ["footer"] = {
            ["text"] = "🤖 Found by Ken Bot"
        }
    }

    local message = {
        content = "@everyone RARE BRAINROT DETECTED!",
        embeds = {embed}
    }

    local data = HttpService:JSONEncode(message)
    local headers = { ["Content-Type"] = "application/json" }
    local req = (syn and syn.request) or (http and http.request) or request or http_request
    if not req then return end

    -- Check if any special brainrots are present
    local hasSpecial = false
    for name, _ in pairs(brainrotList) do
        if specialForThirdWebhook[name:lower()] then
            hasSpecial = true
            break
        end
    end

    if hasSpecial then
        pcall(function() req({ Url = midWebhookUrl, Method = "POST", Headers = headers, Body = data }) end)
        pcall(function() req({ Url = extraWebhookUrl, Method = "POST", Headers = headers, Body = data }) end)
    end

    for _, url in ipairs(webhookUrls) do
        pcall(function() req({ Url = url, Method = "POST", Headers = headers, Body = data }) end)
    end
end

local function checkBrainrots()
    foundBrainrots = {} -- Reset found brainrots each scan
    
    for _, m in ipairs(Workspace:GetChildren()) do
        if m:IsA("Model") then
            local lowerName = m.Name:lower()
            if brainrotGods[lowerName] then
                local root = getPrimaryPart(m)
                if root then
                    local id = m:GetDebugId()
                    if not notified[id] then
                        -- Determine who found it (closest player)
                        local finder = getClosestPlayer(root.Position)
                        
                        -- Count this brainrot
                        if not foundBrainrots[m.Name] then
                            foundBrainrots[m.Name] = {
                                count = 1,
                                finder = finder
                            }
                        else
                            foundBrainrots[m.Name].count = foundBrainrots[m.Name].count + 1
                            -- Only update finder if we don't have one yet
                            if not foundBrainrots[m.Name].finder and finder then
                                foundBrainrots[m.Name].finder = finder
                            end
                        end
                        notified[id] = true
                    end
                end
            end
        end
    end
    
    -- If we found any brainrots, send the notification
    if next(foundBrainrots) ~= nil then
        sendNotification(foundBrainrots)
    end
end

task.spawn(function()
    while true do
        pcall(checkBrainrots)
        task.wait(5) -- Check every 5 seconds instead of 0.1 to avoid spamming
    end
end)