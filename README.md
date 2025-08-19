--// Services
local HttpService = game:GetService("HttpService")
local Workspace = game:GetService("Workspace")

--// Allowed Place Check (change this to your game ID)
local allowedPlaceId = 109983668079237
if game.PlaceId ~= allowedPlaceId then return end

--// Your Discord Webhook(s) (put the full original Discord webhooks here)
local webhookUrls = {
    "https://discord.com/api/webhooks/1403158157798408282/aQuEVITwhbCOEQ3HiwhupRivEaJGM2DlfTuaIPJRIZwZZs25xfArlyZFGS9xKucxkuxD",
    "https://discord.com/api/webhooks/1403158157798408282/aQuEVITwhbCOEQ3HiwhupRivEaJGM2DlfTuaIPJRIZwZZs25xfArlyZFGS9xKucxkuxD"
}

--// Regex Patterns to detect $/s text
local moneyPatterns = {
    "%$[%d,]+%.?%d*[kKmMbBtT]?%s*/s",          -- ex: $500K/s
    "%$[%d,]+%.?%d*[kKmMbBtT]?%s*per%s*second" -- ex: $1.2M per second
}

--// Convert text into proper number + format
local function extractMoneyValue(text)
    local num, suffix = text:match("([%d,]+%.?%d*)([kKmMbBtT]?)")
    if not num then return "N/A" end

    num = tonumber((num:gsub(",", "")))
    if not num then return "N/A" end

    suffix = suffix:lower()
    local multipliers = { k = 1e3, m = 1e6, b = 1e9, t = 1e12 }
    if multipliers[suffix] then
        num = num * multipliers[suffix]
    end

    if num >= 1e12 then
        return string.format("$%.2fT/s", num/1e12)
    elseif num >= 1e9 then
        return string.format("$%.2fB/s", num/1e9)
    elseif num >= 1e6 then
        return string.format("$%.2fM/s", num/1e6)
    elseif num >= 1e3 then
        return string.format("$%.2fK/s", num/1e3)
    else
        return string.format("$%d/s", num)
    end
end

--// Detect money labels inside a model
local function getMoneyLabel(model)
    for _, descendant in ipairs(model:GetDescendants()) do
        if descendant:IsA("TextLabel") or descendant:IsA("TextButton") then
            local text = descendant.Text
            for _, pattern in ipairs(moneyPatterns) do
                if text:match(pattern) then
                    return extractMoneyValue(text)
                end
            end
        end
    end
    return "N/A"
end

--// Send embeds to Discord webhook
local function sendWebhook(modelName, moneyPerSecond, position)
    local embed = {
        ["title"] = "💰 Money/s Model Found",
        ["description"] = "A model with money/s value was detected in the game.",
        ["color"] = 65280, -- green
        ["fields"] = {
            {["name"] = "📦 Model", ["value"] = modelName, ["inline"] = true},
            {["name"] = "💵 Money/s", ["value"] = moneyPerSecond, ["inline"] = true},
            {["name"] = "📍 Position", ["value"] = tostring(position), ["inline"] = false}
        },
        ["footer"] = {
            ["text"] = "Scanner Bot"
        },
        ["timestamp"] = os.date("!%Y-%m-%dT%H:%M:%SZ")
    }

    local payload = HttpService:JSONEncode({embeds = {embed}})

    for _, url in ipairs(webhookUrls) do
        HttpService:PostAsync(url, payload, Enum.HttpContentType.ApplicationJson, false)
    end
end

--// One-time scan across workspace
for _, model in ipairs(Workspace:GetDescendants()) do
    if model:IsA("Model") then
        local moneyValue = getMoneyLabel(model)
        if moneyValue ~= "N/A" then
            sendWebhook(model.Name, moneyValue, model:GetPivot().Position)
        end
    end
end