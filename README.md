repeat task.wait() until workspace:FindFirstChild("Plots")
local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")

-- Only run in this specific Place ID
local allowedPlaceId = 109983668079237 -- <-- Replace with your game's place ID
if game.PlaceId ~= allowedPlaceId then return end

local UnderTen = "https://discord.com/api/webhooks/1403158157798408282/aQuEVITwhbCOEQ3HiwhupRivEaJGM2DlfTuaIPJRIZwZZs25xfArlyZFGS9xKucxkuxD" -- webhook for under 10m
local OverTen = "https://discord.com/api/webhooks/1403467926333427883/5GDaRTMPtKaLwINprYyj_WDMiZPHbyn8OwhCK89nrLmF7n074y6DJFZf8o4dfn-K4qYf"  -- webhook for over 10m
local embedColor = 3447003
local placeId = game.PlaceId

local req = (syn and syn.request) or (http and http.request) or (http_request) or (fluxus and fluxus.request) or request

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
                {["name"] = "Join Link", ["value"] = "[Join Here](https://www.roblox.com/games/start?placeId="..placeId.."&launchData="..jobId..")", ["inline"] = false},
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

for _, v in pairs(workspace:GetDescendants()) do
    if v:IsA("TextLabel") and v.Name == "Generation" then
        local originalText = v.Text
        local value = convertTextToNumber(originalText)
        local playerCount = getPlayerCount()
        
        -- Only send if player count is 6 or 7
        if playerCount >= 6 and playerCount <= 7 and value >= 1000000 then
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
                local petFound, moneyPerSec, tag, mutation, jobId = result, v.Text, v.Parent.Rarity.Text, "None", game.JobId
                
                local mutationTag = v.Parent:FindFirstChild("Mutation")
                if mutationTag and mutationTag.Visible then
                    mutation = mutationTag.Text
                end
                
                local webhookUrl, shouldPing
                if petFound:lower() == "lucky block" then
                    if tag:lower() == "secret" then
                        webhookUrl = OverTen
                        shouldPing = false
                    else
                        webhookUrl = UnderTen
                        shouldPing = false
                    end
                else
                    webhookUrl = value >= 10000000 and OverTen or UnderTen
                    shouldPing = value >= 10000000
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
            else
                warn("Error accessing pet data:", result)
            end
        end
    end
end

for _, webhookData in pairs(webhooksToSend) do
    sendWebhook(webhookData.url, webhookData.petFound, webhookData.moneyPerSec, webhookData.tag, webhookData.mutation, webhookData.jobId, webhookData.pingEveryone, webhookData.playerCount)
    task.wait(0.1)
end

if #webhooksToSend > 0 then
    print("Sent", #webhooksToSend, "webhooks for player count 6–7.")
end
