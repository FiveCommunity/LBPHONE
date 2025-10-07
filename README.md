# 📱 LB Phone - Adaptação para vRP Framework

## 🎯 **O que é este guia?**

Este guia mostra exatamente quais funções do LB Phone foram adaptadas para funcionar com o framework vRP, onde cada função está localizada e como foi implementada.

> **📌 IMPORTANTE:** O framework **Creative Summzerz** é baseado no **vRP**, por isso as adaptações mostradas aqui funcionam para ambos os frameworks. O Creative Summzerz mantém a mesma estrutura e funções do vRP, apenas com melhorias e otimizações.

---

## ⚡ **Configuração Inicial**

### **🔗 Compatibilidade de Frameworks**

| **Framework** | **Config** | **Compatibilidade** |
|---------------|------------|-------------------|
| **Creative Summzerz** | `"vrp"` | ✅ Total (baseado no vRP) |
| **Standalone** | `"standalone"` | ❌ Sem framework ( São todos) |

### **1. Alterar Framework**
```lua
-- Arquivo: config/config.lua
Config.Framework = "standalone"  


### **2. Reiniciar Recurso**
```lua
restart lbphone
```

---

## 📋 **Funções Adaptadas - Server-Side**

### **Arquivo:** `server/custom/frameworks/standalone.lua`

#### **🔑 Funções de Identificação**

**Função:** `GetIdentifier(source)`
- **Onde:** Linha 212-214
- **Adaptação:** `vRP.users_by_source[source].cid`
- **Retorna:** ID único do jogador com prefixo "vrp_"

**Função:** `GetCharacterName(source)`
- **Onde:** Linha 216-220
- **Adaptação:** `vRP.EXT.Identity:getIdentity(user.cid)`
- **Retorna:** Nome e sobrenome do personagem

#### **💰 Funções Bancárias**

**Função:** `GetBalance(source)`
- **Onde:** Linha 236-238
- **Adaptação:** `user:getBank()`
- **Retorna:** Saldo bancário do jogador

**Função:** `AddMoney(source, amount)`
- **Onde:** Linha 240-242
- **Adaptação:** `user:giveBank(amount)`
- **Retorna:** Adiciona dinheiro ao banco

**Função:** `RemoveMoney(source, amount)`
- **Onde:** Linha 244-246
- **Adaptação:** `user:setBank(math.abs(bank - amount))`
- **Retorna:** Remove dinheiro do banco

#### **🎒 Funções de Itens**

**Função:** `HasItem(source, itemName)`
- **Onde:** Linha 224-234
- **Adaptação:** `user:tryTakeItem(item, 1, true, true)`
- **Retorna:** Verifica se jogador tem o item

**Função:** `registerItem(item, name, description)`
- **Onde:** Linha 87-89
- **Adaptação:** `vRP.EXT.Inventory:defineItem(item, name, description, nil, 0.2)`
- **Retorna:** Registra item como usável

#### **👮 Funções de Permissões**

**Função:** `IsAdmin(source)`
- **Onde:** Linha 248-250
- **Adaptação:** `user:hasGroup("admin")`
- **Retorna:** Verifica se jogador é admin

**Função:** `GetJob(source)`
- **Onde:** Linha 252-254
- **Adaptação:** `user:getGroupByType("job")`
- **Retorna:** Job atual do jogador

**Função:** `GetEmployees(job)`
- **Onde:** Linha 256-258
- **Adaptação:** Loop através de `vRP.users_by_source`
- **Retorna:** Lista de funcionários do job

#### **🚗 Funções de Veículos**

**Função:** `GetPlayerVehicles(source)`
- **Onde:** Linha 260-262
- **Adaptação:** `user:getVehicles()` e `user:getVehicleState(model)`
- **Retorna:** Lista de veículos do jogador

**Função:** `GetVehicle(source, model)`
- **Onde:** Linha 264-266
- **Adaptação:** `user:getVehicleState(model)`
- **Retorna:** Dados específicos do veículo

---

## 📱 **Funções Adaptadas - Client-Side**

### **Arquivo:** `client/custom/frameworks/standalone.lua`

#### **🚗 Funções de Veículos**

**Função:** `ApplyVehicleMods(vehicle, vehicleData)`
- **Onde:** Linha 109-111
- **Adaptação:** `vRP.EXT.Garage:setVehicleState(vehicle, vehicleData)`
- **Retorna:** Aplica modificações no veículo

**Função:** `CreateFrameworkVehicle(vehicleData, coords)`
- **Onde:** Linha 113-123
- **Adaptação:** `lbphone.spawnVehicle(vehicleData, coords)`
- **Retorna:** Cria veículo do framework

#### **🎒 Funções de Itens**

**Função:** `HasItem(itemName)`
- **Onde:** Linha 100-104
- **Adaptação:** `return true` (verificação server-side)
- **Retorna:** Verifica item no cliente

#### **👤 Funções de Spawn**

**Função:** `playerSpawn()`
- **Onde:** Linha 23-29
- **Adaptação:** `TriggerEvent("lb-phone:vrp:firstSpawn")`
- **Retorna:** Detecta quando jogador spawna

**Função:** `FirstSpawn()`
- **Onde:** Linha 17-21
- **Adaptação:** `hasSpawned = true`
- **Retorna:** Marca primeiro spawn

---

## 🔧 **Sistema de Extensão vRP**

### **Server-Side Extension**
```lua
-- Arquivo: server/custom/frameworks/standalone.lua
-- Linha: 1-178

local lbphone = class("lbphone", vRP.Extension)

function lbphone:__construct()
    vRP.Extension.__construct(self)
end

lbphone.event = {}
lbphone.tunnel = {}
lbphone.proxy = {}

-- Registra extensão
vRP:registerExtension(lbphone)
```

### **Client-Side Extension**
```lua
-- Arquivo: client/custom/frameworks/standalone.lua
-- Linha: 5-63

local lbphone = class(name, vRP.Extension)

lbphone.user = class("User")
lbphone.proxy = {}
lbphone.event = {}
lbphone.tunnel = {}

-- Registra extensão
vRP:registerExtension(lbphone)
```

---

## 📊 **Mapeamento de Funções**

### **Funções do LB Phone → Funções vRP**

| **LB Phone** | **vRP Implementation** | **Arquivo** | **Linha** |
|--------------|------------------------|-------------|-----------|
| `GetIdentifier()` | `vRP.users_by_source[source].cid` | server/standalone.lua | 212-214 |
| `GetCharacterName()` | `vRP.EXT.Identity:getIdentity()` | server/standalone.lua | 216-220 |
| `GetBalance()` | `user:getBank()` | server/standalone.lua | 236-238 |
| `AddMoney()` | `user:giveBank()` | server/standalone.lua | 240-242 |
| `RemoveMoney()` | `user:setBank()` | server/standalone.lua | 244-246 |
| `HasItem()` | `user:tryTakeItem()` | server/standalone.lua | 224-234 |
| `IsAdmin()` | `user:hasGroup("admin")` | server/standalone.lua | 248-250 |
| `GetJob()` | `user:getGroupByType("job")` | server/standalone.lua | 252-254 |
| `GetEmployees()` | Loop `vRP.users_by_source` | server/standalone.lua | 256-258 |
| `GetPlayerVehicles()` | `user:getVehicles()` | server/standalone.lua | 260-262 |
| `GetVehicle()` | `user:getVehicleState()` | server/standalone.lua | 264-266 |
| `ApplyVehicleMods()` | `vRP.EXT.Garage:setVehicleState()` | client/standalone.lua | 109-111 |
| `CreateFrameworkVehicle()` | `lbphone.spawnVehicle()` | client/standalone.lua | 113-123 |

---

## 🎯 **Códigos Completos das Adaptações**

### **🖥️ Server-Side - Arquivo: `server/custom/frameworks/vrp2.lua`**

#### **🔑 Sistema de Extensão vRP**
```lua
if GetCurrentResourceName() == "vrp" then
    ---@diagnostic disable: undefined-global
    local lbphone = class("lbphone", vRP.Extension)

    function lbphone:__construct()
        vRP.Extension.__construct(self)
    end

    lbphone.event = {}
    lbphone.tunnel = {}
    lbphone.proxy = {}

    function lbphone.proxy:getIdentifier(source)
        local user = vRP.users_by_source[source]
        if user and user.cid then
            return "vrp_" .. user.cid
        end
    end

    function lbphone.proxy:getCharacterName(source)
        local user = vRP.users_by_source[source]
        local identity = user and user.cid and vRP.EXT.Identity:getIdentity(user.cid)
        if identity then
            return identity.firstname, identity.name
        else
            return GetPlayerName(source), ""
        end
    end

    function lbphone.proxy:hasItem(source, item)
        local user = vRP.users_by_source[source]
        if user then
            return user:tryTakeItem(item, 1, true, true)
        else
            return false
        end
    end

    function lbphone.proxy:getBank(source)
        local user = vRP.users_by_source[source]
        if user then
            return user:getBank()
        end
    end

    function lbphone.proxy:giveBank(source, amount)
        local user = vRP.users_by_source[source]
        if user and amount > 0 then
            user:giveBank(amount)
            return true
        end
        return false
    end

    function lbphone.proxy:removeBank(source, amount)
        local user = vRP.users_by_source[source]
        local bank = user and user:getBank()
        if user and bank and amount > 0 and bank >= amount then
            user:setBank(math.abs(bank - amount))
            return true
        end
        return false
    end

    function lbphone.proxy:isAdmin(source)
        local user = vRP.users_by_source[source]
        return user and user:hasGroup("admin")
    end

    function lbphone.proxy:getJob(source)
        local user = vRP.users_by_source[source]
        return user and user:getGroupByType("job") or "citizen"
    end

    function lbphone.proxy:registerItem(item, name, description)
        vRP.EXT.Inventory:defineItem(item, name, description, nil, 0.2)
    end

    function lbphone.event:playerSpawn(user, firstSpawn)
        print("PLAYER SPAWNED!", user, firstSpawn)
    end

    function lbphone.event:characterLoad(user)
        print("character load", user)
    end

    function lbphone.event:characterUnload(user)
        print("character unload", user)
    end

    function lbphone.proxy:getPlayerVehicles(source)
        local user = vRP.users_by_source[source]
        if not user then
            return {}
        end

        ---@type { [string]: number }[]
        local vehicles = user:getVehicles()
        local toSend = {}

        for model, stored in pairs(vehicles) do
            local vehicleState = user:getVehicleState(model)
            if not vehicleState then
                vehicleState = {
                    customization = {},
                    condition = {}
                }
            end

            toSend[#toSend+1] = {
                plate = model,
                type = "car",
                model = model,
                location = stored == 1 and "Garage" or "out",
                statistics = {
                    engine = vehicleState.condition.engine_health or 1000.0,
                    body = vehicleState.condition.body_health or 1000.0
                }
            }
        end

        return toSend
    end

    function lbphone.proxy:getVehicle(source, model)
        local user = vRP.users_by_source[source]
        if not user then
            return
        end

        local vehicle = user:getVehicleState(model)
        if not vehicle then
            return
        end

        vehicle.plate = model
        vehicle.model = GetHashKey(model)
        vehicle.type = "car"

        return vehicle
    end

    function lbphone.proxy:getEmployees(job)
        local users = vRP.users_by_source
        local employees = {}

        for src, user in pairs(users) do
            if user and user:getGroupByType("job") == job then
                employees[#employees+1] = src
            end
        end

        return employees
    end

    vRP:registerExtension(lbphone)
    print("^6[LB Phone]: ^2[INFO]^7: Loaded LB Phone vRP extension.")
    return
end
```

#### **🔧 Interface Proxy/Tunnel**
```lua
---@diagnostic disable: deprecated
if Config.Framework ~= "vrp2" then
    return
end

local utils = LoadResourceFile("vrp", "lib/utils.lua")
if not utils then
    print("^6[LB Phone] ^1[ERROR]^7: Failed to load vrp (could not find vrp/lib/utils.lua)")
    return
end

load(utils)()

local proxy = module("vrp", "lib/Proxy")
local tunnel = module("vrp", "lib/Tunnel")

if not proxy or not tunnel then
    print("^6[LB Phone] ^1[ERROR]^7: Failed to load vrp (could not load proxy/tunnel module)")
    return
end

local vRP = proxy.getInterface("vRP")
vRP.loadScript(GetCurrentResourceName(), "server/custom/frameworks/vrp2")
local lbphone = proxy.getInterface("vRP.EXT.lbphone")

lbphone.registerItem(Config.Item.Name, "Phone", "A phone")

---@param source number
---@return string | nil
function GetIdentifier(source)
    return lbphone.getIdentifier(source)
end

---@param source number
---@return string firstname
---@return string lastname
function GetCharacterName(source)
    return lbphone.getCharacterName(source)
end

---@param source number
---@param itemName string
function HasItem(source, itemName)
    if GetResourceState("ox_inventory") == "started" then
        return (exports.ox_inventory:Search(source, "count", itemName) or 0) > 0
    end
    return lbphone.hasItem(source, itemName)
end

---@param source number
---@return integer
function GetBalance(source)
    return lbphone.getBank(source)
end

---@param source number
---@param amount integer
---@return boolean # Success
function AddMoney(source, amount)
    return lbphone.giveBank(source, amount)
end

---@param source number
---@param amount integer
---@return boolean # Success
function RemoveMoney(source, amount)
    return lbphone.removeBank(source, amount)
end

---@param source number
---@return boolean
function IsAdmin(source)
    return lbphone.isAdmin(source)
end

---@param source number
---@return string
function GetJob(source)
    return lbphone.getJob(source)
end

---Get an array of player sources with a specific job
---@param job string
---@return number[] employees
function GetEmployees(job)
    return lbphone.getEmployees(job)
end

function RefreshCompanies()
    for i = 1, #Config.Companies.Services do
        local jobData = Config.Companies.Services[i]
        jobData.open = #lbphone.getEmployees(jobData.job) > 0
    end
end

function GetPlayerVehicles(source)
    return lbphone.getPlayerVehicles(source)
end

function GetVehicle(source, model)
    return lbphone.getVehicle(source, model)
end
```

### **📱 Client-Side - Arquivo: `client/custom/frameworks/vrp2.lua`**

#### **🔧 Sistema de Extensão vRP (Client)**
```lua
---@diagnostic disable: deprecated

local name = "lbphone" .. 1

if GetCurrentResourceName() == "vrp" then
    print("loaded in vrp")
    ---@diagnostic disable: undefined-global
    local lbphone = class(name, vRP.Extension)

    lbphone.user = class("User")
    lbphone.proxy = {}
    lbphone.event = {}
    lbphone.tunnel = {}

    local hasSpawned = false

    local function FirstSpawn()
        hasSpawned = true
        TriggerEvent("lb-phone:vrp:firstSpawn")
    end

    function lbphone.event:playerSpawn()
        if hasSpawned then
            return
        end
        FirstSpawn()
    end

    function lbphone.tunnel:setCharacterId(id)
        print("got character id:", id)
    end

    if vRP.EXT.Base.cid then
        FirstSpawn()
    end

    function lbphone.proxy:spawnVehicle(vehicleData, coords)
        local model = vehicleData.model

        RequestModel(model)

        while not HasModelLoaded(model) do
            Wait(0)
        end

        local vehicle = CreateVehicle(model, coords.x, coords.y, coords.z, 0.0, true, false)

        SetVehicleOnGroundProperly(vehicle)
        SetModelAsNoLongerNeeded(model)

        return vehicle
    end

    function lbphone.proxy:applyVehicleMods(vehicle, vehicleData)
        vRP.EXT.Garage:setVehicleState(vehicle, vehicleData)
    end

    vRP:registerExtension(lbphone)
    return
end
```

#### **🔧 Interface Client-Side**
```lua
Wait(500)

if Config.Framework ~= "vrp" then
    return
end

local utils = LoadResourceFile("vrp", "lib/utils.lua")

if not utils then
    print("^6[LB Phone] ^1[ERROR]^7: Failed to load vrp (could not find vrp/lib/utils.lua)")
    return
end

load(utils)()

local proxy = module("vrp", "lib/Proxy")
local tunnel = module("vrp", "lib/Tunnel")

if not proxy or not tunnel then
    print("^6[LB Phone] ^1[ERROR]^7: Failed to load vrp (could not load proxy/tunnel module)")
    return
end

local vRP = proxy.getInterface("vRP")

RegisterNetEvent("lb-phone:vrp:firstSpawn", function()
    loaded = true
end)

vRP.loadScript(GetCurrentResourceName(), "client/custom/frameworks/vrp2")

local lbphone = proxy.getInterface("vRP.EXT." .. name)

-- TODO: check if has phone item

---@param itemName string
---@return boolean
function HasItem(itemName)
    return true
end

---Apply vehicle mods
---@param vehicle number
---@param vehicleData table
function ApplyVehicleMods(vehicle, vehicleData)
    lbphone.applyVehicleMods(vehicle, vehicleData)
end

---Create a vehicle and apply vehicle mods
---@param vehicleData table
---@param coords vector3
---@return number? vehicle
function CreateFrameworkVehicle(vehicleData, coords)
    local vehicle = lbphone.spawnVehicle(vehicleData, coords)

    ApplyVehicleMods(vehicle, vehicleData)

    return vehicle
end
```

### **🔧 Correções de Bugs**

#### **Crypto Timeout Fix - `client/apps/other/crypto.lua`**
```lua
-- Initialize crypto data
CreateThread(function()
    while not FrameworkLoaded do
        Wait(0)
    end

    -- Add timeout protection
    local timeout = 0
    local maxTimeout = 30000 -- 30 seconds
    
    local coins = nil
    local success = false
    
    -- Try to get crypto data with timeout
    CreateThread(function()
        coins = AwaitCallback("crypto:get")
        success = true
    end)
    
    -- Wait for callback or timeout
    while not success and timeout < maxTimeout do
        Wait(100)
        timeout = timeout + 100
    end
    
    cryptoData = {}
    
    if success and coins and type(coins) == "table" then
        for _, coin in pairs(coins) do
            if coin and type(coin) == "table" then
                table.insert(cryptoData, coin)
            end
        end
        debugprint("fetched coins successfully")
    else
        if not success then
            debugprint("crypto:get timed out after", timeout, "ms")
        else
            debugprint("crypto:get returned invalid data:", type(coins))
        end
        -- Initialize with empty data to prevent errors
        cryptoData = {}
    end
end)
```

#### **Crypto Server Fix - `server/apps/other/crypto.lua`**
```lua
-- Inicia a atualização das moedas periodicamente
CreateThread(function()
    -- Initial fetch
    UpdateCryptoCoins()
    CryptoData.hasFetched = true
    TriggerClientEvent("phone:crypto:updateCoins", -1, CryptoData.coins)
    
    -- Periodic updates
    while true do
        Wait(Config.Refresh)
        UpdateCryptoCoins()
        TriggerClientEvent("phone:crypto:updateCoins", -1, CryptoData.coins)
    end
end)

-- Callbacks
RegisterCallback("crypto:get", function(source)
    local identifier = GetIdentifier(source)
    
    if not identifier then
        debugprint("crypto:get - No identifier found for source:", source)
        return {}
    end
    
    -- Wait for initial data fetch with timeout
    local timeout = 0
    local maxTimeout = 10000 -- 10 seconds
    
    while not CryptoData.hasFetched and timeout < maxTimeout do
        Wait(100)
        timeout = timeout + 100
    end
    
    if not CryptoData.hasFetched then
        debugprint("crypto:get - Timeout waiting for crypto data")
        return {}
    end
    
    -- Get player's crypto holdings
    local holdings = {}
    local success, result = pcall(function()
        return MySQL.query.await(
            "SELECT coin, amount, invested FROM phone_crypto WHERE id = ?",
            {identifier}
        )
    end)
    
    if success and result then
        holdings = result
    else
        debugprint("crypto:get - Failed to get holdings for identifier:", identifier)
    end
    
    -- Create a copy of the coin data
    local coinData = {}
    if CryptoData.coins and type(CryptoData.coins) == "table" then
        coinData = table.deep_clone(CryptoData.coins)
    end
    
    -- Add player's holdings to the data
    for _, holding in ipairs(holdings) do
        if holding and coinData[holding.coin] then
            coinData[holding.coin].owned = holding.amount
            coinData[holding.coin].invested = holding.invested
        end
    end
    
    return coinData
end)
```

---

## 🔧 **Solução de Problemas**

### **❌ Erro: "Framework not loaded"**
**Causa:** vRP não está carregado
**Solução:** Verificar se `Config.Framework = "standalone"` está correto

### **❌ Erro: "No such export getInterface"**
**Causa:** Tentando usar vRP 1.0 com código vRP 2.0
**Solução:** Usar `Config.Framework = "vrp"` para vRP 1.0

### **❌ Erro: "Callback crypto:get timed out"**
**Causa:** Timeout no sistema de crypto
**Solução:** ✅ **Já corrigido** - Adicionado timeout protection

---

## 📋 **Checklist de Instalação**

### **Antes de começar:**
- [ ] vRP framework instalado e funcionando
- [ ] Extensões vRP necessárias (Identity, Inventory, Garage)
- [ ] Banco de dados configurado
- [ ] LB Phone baixado

### **Passos de instalação:**
1. [ ] Alterar `Config.Framework = "standalone"` em `config/config.lua`
2. [ ] Reiniciar o recurso: `restart lbphone`
3. [ ] Testar se o telefone abre
4. [ ] Verificar se as funções básicas funcionam

### **Testes recomendados:**
- [ ] Abrir/fechar telefone
- [ ] Verificar saldo bancário
- [ ] Testar sistema de itens
- [ ] Verificar jobs e permissões
- [ ] Testar sistema de veículos

---

## 🎯 **Resumo Final**

**O que foi feito:**
- ✅ **13 funções principais adaptadas** para vRP
- ✅ **Sistema de extensão nativo** implementado
- ✅ **Compatibilidade vRP 1.0 e 2.0** garantida
- ✅ **Correção de bugs** conhecidos
- ✅ **Documentação completa** de cada função

**Para usar:**
1. Altere `Config.Framework = "standalone"` 
2. Reinicie o recurso
3. Pronto!

**Suporte:**
FIVE COMMUNITY
