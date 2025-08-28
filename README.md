repeat task.wait() until workspace:FindFirstChild("Plots")

local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")

-- ✅ Replace with your own PlaceId
local allowedPlaceId = 109983668079237
if game.PlaceId ~= allowedPlaceId then return end

-- ✅ Webhooks
local UnderTen = "https://discord.com/api/webhooks/1403158157798408282/aQuEVITwhbCOEQ3HiwhupRivEaJGM2DlfTuaIPJRIZwZZs25xfArlyZFGS9xKucxkuxD" -- under 1M
local Between500AndOneM = "https://discord.com/api/webhooks/1410650201739366442/vqkmydnw98-lEpalqBlffNTwhPDGnKUUABUH21QuRzNaIiRtLlumw4OE2W1JRxJmWUHA" -- 500–1M
local OverTen = "https://discord.com/api/webhooks/1403467926333427883/5GDaRTMPtKaLwINprYyj_WDMiZPHbyn8OwhCK89nrLmF7n074y6DJFZf8o4dfn-K4qYf" -- over 10M

local embedColor = 3447003
local placeId = game.PlaceId

-- Build Chilli Hub join link
local function getChilliHubJoinLink(jobId)
    return string.format(
        "https://chillihub1.github.io/chillihub-joiner/?placeId=%s&gameInstanceId=%s",
        tostring(placeId),
        tostring(jobId)
    )
end

-- Send Webhook
local function sendWebhook(url, petFound, moneyPerSec, tag, mutation, jobId, pingEveryone, playerCount)
    local data = {
        ["username"] = "Ken Hub",
        ["embeds"] = {{
            ["title"] = "Ken Hub",
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
    HttpService:PostAsync(url, jsonData, Enum.HttpContentType.ApplicationJson)
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
    
    return (tonumber(text) or 0) * multiplier
end

-- Count players
local function getPlayerCount()
    return #Players:GetPlayers()
end

-- Collect and send webhooks
local webhooksToSend = {}

for _, v in pairs(workspace:GetDescendants()) do
    if v:IsA("TextLabel") and v.Name == "Generation" then
        local originalText = v.Text
        local value = convertTextToNumber(originalText)
        local playerCount = getPlayerCount()
        
        if playerCount >= 6 and playerCount <= 8 and value >= 500 then
            local petFound = v.Parent.DisplayName.Text
            local moneyPerSec = v.Text
            local tag = v.Parent.Rarity.Text
            local jobId = game.JobId
            
            local mutation = "None"
            local mutationTag = v.Parent:FindFirstChild("Mutation")
            if mutationTag and mutationTag.Visible then
                mutation = mutationTag.Text
            end
            
            local webhookUrl, shouldPing
            if value >= 500 and value < 1_000_000 then
                webhookUrl = Between500AndOneM
                shouldPing = false
            elseif value >= 10_000_000 then
                webhookUrl = OverTen
                shouldPing = true
            else
                webhookUrl = UnderTen
                shouldPing = false
            end
            
            table.insert(webhooksToSend, {
                url = webhookUrl,
                petFound = petFound,
                moneyPerSec = moneyPerSec,
                tag = tag,
                mutation = mutation,
                jobId = jobId,
                pingEveryone = shouldPing,
                playerCount = playerCount
            })
        end
    end
end

for _, webhookData in pairs(webhooksToSend) do
    sendWebhook(
        webhookData.url,
        webhookData.petFound,
        webhookData.moneyPerSec,
        webhookData.tag,
        webhookData.mutation,
        webhookData.jobId,
        webhookData.pingEveryone,
        webhookData.playerCount
    )
    task.wait(0.1)
end

if #webhooksToSend > 0 then
    print("Sent", #webhooksToSend, "webhooks (500+, includes 500–1M new range).")
end
