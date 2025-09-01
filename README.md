repeat task.wait() until workspace:FindFirstChild("Plots")
local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")

-- Only run in these specific Place IDs
local allowedPlaceIds = {109983668079237, 96342491571673}
if not table.find(allowedPlaceIds, game.PlaceId) then return end

-- existing webhooks (kept names so older references work)
local UnderTen = "https://discord.com/api/webhooks/1403158157798408282/aQuEVITwhbCOEQ3HiwhupRivEaJGM2DlfTuaIPJRIZwZZs25xfArlyZFGS9xKucxkuxD" -- webhook for under 10m (original)
local OverTen = "https://discord.com/api/webhooks/1403467926333427883/5GDaRTMPtKaLwINprYyj_WDMiZPHbyn8OwhCK89nrLmF7n074y6DJFZf8o4dfn-K4qYf"  -- webhook for over 10m (original)

-- NEW webhooks the user provided / repurposed
local UnderTen_Extra = "https://discord.com/api/webhooks/1400192912851206234/wX-_vo9jsNRiU_rquaaMEhqwQBHKlQ83XVnoWi2qmopo4av3lyGrl0JglIY3G2ndHn_N" -- new under 10m webhook
-- The webhook that used to be "Under500k" is now repurposed for 500k-1M:
local Between500k_1M = "https://discord.com/api/webhooks/1409589466703724574/aVfAQJRJ9yXc9uPsR9_FpC1viI9RIZyRtGCqomWnehlXEvZnC2gQLeGdm4l23WU1jZ5x"
-- The new webhook you gave — added as the extra for 500k-1M:
local Between500k_1M_Extra = "https://discord.com/api/webhooks/1410650201739366442/vqkmydnw98-lEpalqBlffNTwhPDGnKUUABUH21QuRzNaIiRtLlumw4OE2W1JRxJmWUHA"
local OverTen_Extra = "https://discord.com/api/webhooks/1399949372304654356/9bOvZ7MJf7lBYpXy9YzXVD2nt1Xb6RWcd4l1RVmbymF_WC-BgG-IYdQsSopyOdEVhFfb" -- new over 10m+ webhook

-- 🔁 Your Replit API endpoint
local replitApiEndpoint = "https://8d93f3f5-a95f-4cc1-84d9-5d3dfb8650f5-00-3iq0togrerm7d.riker.replit.dev/api"

local embedColor = 3447003
local placeId = game.PlaceId

local req = (syn and syn.request) or (http and http.request) or (http_request) or (fluxus and fluxus.request) or request

-- Build Chilli Hub join link
local function getChilliHubJoinLink(jobId)
    return string.format(
        "https://chillihub1.github.io/chillihub-joiner/?placeId=%s&gameInstanceId=%s",
        tostring(placeId),
        tostring(jobId)
    )
end

-- Send to Replit API for GUI updates
local function sendToReplitAPI(petsFound, jobId, playerCount)
    if #petsFound == 0 then return end
    
    local jsonData = HttpService:JSONEncode({
        ["pets"] = petsFound,
        ["jobId"] = jobId,
        ["placeId"] = game.PlaceId,
        ["timestamp"] = os.time(),
        ["isPrivate"] = false,
        ["playerCount"] = playerCount,
        ["maxPlayers"] = Players.MaxPlayers
    })

    if req then
        local success, response = pcall(function()
            return req({
                Url = replitApiEndpoint,
                Method = "POST",
                Headers = {
                    ["Content-Type"] = "application/json",
                    ["Authorization"] = "Bearer roblox-pet-scanner"
                },
                Body = jsonData
            })
        end)
        
        if success and response then
            print("✅ Successfully sent " .. #petsFound .. " pets to Replit API")
        else
            warn("❌ Failed to send pets to Replit API")
        end
    else
        warn("❌ HTTP request function not available")
    end
end

-- Send Webhook
local function sendWebhook(url, petFound, moneyPerSec, tag, mutation, jobId, pingEveryone, playerCount)
    local data = {
        ["username"] = "KenGer Hub/Ken Hub",
        ["embeds"] = {{
            ["title"] = "KenGer Hub/Ken Hub",
            ["description"] = petFound,
            ["color"] = embedColor,
            ["fields"] = {
                {["name"] = "💸 Money per Sec", ["value"] = moneyPerSec, ["inline"] = true},
                {["name"] = "🏷️ Tag", ["value"] = tag, ["inline"] = true},
                {["name"] = "🧬 Mutation", ["value"] = mutation, ["inline"] = true},
                {["name"] = "👥 Player Count", ["value"] = tostring(playerCount) .. "/8", ["inline"] = true},
                {["name"] = "Join Link", ["value"] = "[Join Here]("..getChilliHubJoinLink(jobId)..")", ["inline"] = false},
                {["name"] = "🆔 Job ID (Mobile)", ["value"] = "``"..jobId.."``", ["inline"] = false},
                {["name"] = "🆔 Job ID (PC)", ["value"] = "```"..jobId.."```", ["inline"] = false},
                {["name"] = "Join Server",
                 ["value"] = string.format("```lua\ngame:GetService('TeleportService'):TeleportToPlaceInstance(%d, '%s', game.Players.LocalPlayer)\n```", placeId, jobId),
                 ["inline"] = false
                }
            }
        }}
    }
    
    if pingEveryone then
        data["content"] = "@everyone"
    end
    
    local jsonData = HttpService:JSONEncode(data)
    
    req({
        Url = url,
        Method = "POST",
        Headers = {["Content-Type"] = "application/json"},
        Body = jsonData
    })
end

-- Convert money text to number
local function convertTextToNumber(text)
    if not text:find("/s") then
        return 0
    end
    
    text = text:gsub("%$", ""):gsub("/s", ""):gsub("%s+", "")
    
    local multiplier = 1
    
    if text:upper():find("K$") then
        multiplier = 1e3
        text = text:gsub("[Kk]$", "")
    elseif text:upper():find("M$") then
        multiplier = 1e6
        text = text:gsub("[Mm]$", "")
    elseif text:upper():find("B$") then
        multiplier = 1e9
        text = text:gsub("[Bb]$", "")
    elseif text:upper():find("T$") then
        multiplier = 1e12
        text = text:gsub("[Tt]$", "")
    end
    
    local num = tonumber(text) or 0
    return num * multiplier
end

-- Count players
local function getPlayerCount()
    return #Players:GetPlayers()
end

-- Collect and send webhooks
local webhooksToSend = {}
local petsForReplit = {} -- New: collect pets for Replit API

for _, v in pairs(workspace:GetDescendants()) do
    if v:IsA("TextLabel") and v.Name == "Generation" then
        local originalText = v.Text
        local value = convertTextToNumber(originalText)
        local playerCount = getPlayerCount()
        
        -- Only send if player count is 6–8 and value >= 1M (kept original guard)
        if playerCount >= 6 and playerCount <= 8 and value >= 1000000 then
            local success, result = pcall(function()
                local petFound = v.Parent.DisplayName.Text
                local moneyPerSec = v.Text
                local tag = v.Parent.Rarity.Text
                local jobId = game.JobId
                
                local mutation = "None"
                local mutationTag = v.Parent:FindFirstChild("Mutation")
                if mutationTag and mutationTag.Visible then
                    mutation = mutationTag.Text
                end
                
                return petFound, moneyPerSec, tag, mutation, jobId
            end)
            
            if success then
                -- NOTE: 'result' from the pcall is the first return value; since multiple values were returned,
                -- we will gather data directly again to avoid confusion
                local petFound = v.Parent.DisplayName.Text
                local moneyPerSec = v.Text
                local tag = v.Parent.Rarity.Text
                local jobId = game.JobId
                
                local mutation = "None"
                local mutationTag = v.Parent:FindFirstChild("Mutation")
                if mutationTag and mutationTag.Visible then
                    mutation = mutationTag.Text
                end
                
                -- Format pet info for Replit API
                local petInfo = string.format("%s (%s, %s)", petFound, moneyPerSec, tag)
                if mutation ~= "None" then
                    petInfo = petInfo .. " [" .. mutation .. "]"
                end
                table.insert(petsForReplit, petInfo)
                
                -- Determine which webhook(s) to send to
                local webhookUrls = {}
                local shouldPing = false
                
                if petFound:lower() == "lucky block" then
                    if tag:lower() == "secret" then
                        -- secret lucky block: go to over ten (both existing and extra), no ping
                        webhookUrls = { OverTen, OverTen_Extra }
                        shouldPing = false
                    else
                        -- non-secret lucky block: under ten (both existing and extra)
                        webhookUrls = { UnderTen, UnderTen_Extra }
                        shouldPing = false
                    end
                else
                    if value >= 10000000 then
                        -- over 10M -> send to both over-ten webhooks, ping everyone
                        webhookUrls = { OverTen, OverTen_Extra }
                        shouldPing = true
                    elseif value >= 500000 and value < 1000000 then
                        -- NEW: 500k to 1M -> send to both Between500k_1M webhooks (repurposed + new)
                        webhookUrls = { Between500k_1M, Between500k_1M_Extra }
                        shouldPing = false
                    else
                        -- everything else under 500k and <10M -> send to both under-10M webhooks (fallback)
                        webhookUrls = { UnderTen, UnderTen_Extra }
                        shouldPing = false
                    end
                end
                
                -- enqueue each webhook individually
                for _, url in ipairs(webhookUrls) do
                    table.insert(webhooksToSend, {
                        url = url,
                        petFound = petFound,
                        moneyPerSec = moneyPerSec,
                        tag = tag,
                        mutation = mutation,
                        jobId = jobId,
                        pingEveryone = shouldPing,
                        playerCount = playerCount
                    })
                end
            else
                warn("Error accessing pet data:", result)
            end
        end
    end
end

-- Send to Replit API first (for GUI updates)
if #petsForReplit > 0 then
    sendToReplitAPI(petsForReplit, game.JobId, getPlayerCount())
end

-- Send original Discord webhooks
for _, webhookData in pairs(webhooksToSend) do
    sendWebhook(webhookData.url, webhookData.petFound, webhookData.moneyPerSec, webhookData.tag, webhookData.mutation, webhookData.jobId, webhookData.pingEveryone, webhookData.playerCount)
    task.wait(0.1)
end

if #webhooksToSend > 0 then
    print("Sent", #webhooksToSend, "webhooks for player count 6–8.")
    print("Also sent", #petsForReplit, "pets to Replit API for GUI updates.")
end