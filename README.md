repeat task.wait() until workspace:FindFirstChild("Plots")
local HttpService = game:GetService("HttpService")
local Players = game:GetService("Players")

-- Only run in this specific Place ID
local allowedPlaceId = 109983668079237 -- <-- Replace with your game's place ID
if game.PlaceId ~= allowedPlaceId then return end

local UnderTen = "https://discord.com/api/webhooks/1403158157798408282/aQuEVITwhbCOEQ3HiwhupRivEaJGM2DlfTuaIPJRIZwZZs25xfArlyZFGS9xKucxkuxD" -- webhook for under 10m
local OverTen = "https://discord.com/api/webhooks/1403467926333427883/5GDaRTMPtKaLwINprYyj_WDMiZPHbyn8OwhCK89nrLmF7n074y6DJFZf8o4dfn-K4qYf"  -- webhook for over 10m

-- 🔗 Your Replit API endpoint
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
