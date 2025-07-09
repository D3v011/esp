-- Lista enorme de possíveis RemoteEvents para recompensas e dinheiro, especialmente para jogos de RP
local possibleEvents = {
    -- Recompensas gerais
    "RedEvent", "RewardEvent", "GetReward", "ClaimReward", "GiveReward", "RemoteEvent",
    "CollectReward", "GiftEvent", "PrizeEvent", "RewardRemote", "DailyReward", "DailyGift",
    "Reward", "Rewards", "RewardHandler", "RewardSystem", "RewardTrigger", "SpecialReward",
    "SecretReward", "Reward4", "EventReward", "Rewarded", "RedeemReward", "RewardCode",
    "RewardClaim", "AwardReward", "GivePrize", "ClaimPrize", "PrizeRemote", "GiftRemote",
    "CodeReward", "VIPReward", "LoginReward", "RewardEventHandler", "RewardDispenser",
    "ChestReward", "BoxReward", "SpinReward", "SpinEvent", "SpinPrize", "SpinTheWheel",
    "WheelReward", "WheelEvent", "LootReward", "LootEvent", "CrateReward", "CrateEvent",
    "PresentReward", "PresentEvent", "GiftClaim", "GiftReceive", "PromoReward", "PromoEvent",
    "PromoCodeReward", "PromoCodeEvent", "RewardButton", "RewardTriggerEvent", "RewardPush",
    "RewardRemoteEvent", "RewardFire", "RewardActivate", "RewardGrant", "CollectPrize",
    "RewardCollect", "RewardCollection", "RewardTaker", "RewardGiver", "AwardEvent",
    "AwardRemote", "TrophyReward", "TrophyEvent", "MedalReward", "MedalEvent", "RankReward",
    -- Dinheiro, moedas, cash, gems, etc.
    "CashReward", "CoinReward", "GemReward", "DiamondReward", "TokenReward", "CurrencyReward",
    "MoneyReward", "MoneyEvent", "MoneyRemote", "GiveMoney", "MoneyHandler", "AddMoney",
    "GiveCash", "CashEvent", "CashRemote", "CashHandler", "AddCash", "CashoutEvent",
    "AddCoins", "GiveCoins", "CoinEvent", "CoinRemote", "CoinHandler", "AddGems",
    "GiveGems", "GemsEvent", "GemsRemote", "GemsHandler", "AddDiamonds", "GiveDiamonds",
    "DiamondsEvent", "DiamondsRemote", "DiamondsHandler", "GiveTokens", "TokensEvent",
    "TokensRemote", "TokensHandler", "AddCurrency", "GiveCurrency", "CurrencyEvent",
    "CurrencyRemote", "CurrencyHandler", "BalanceEvent", "WalletEvent", "BankEvent",
    "DepositEvent", "WithdrawEvent", "PayoutEvent", "PayEvent", "EarnEvent", "PayReward",
    "BankRemote", "WalletRemote", "DepositRemote", "WithdrawRemote", "PayoutRemote",
    "PayRemote", "EarnRemote",
    -- Termos em português comuns em RP
    "DinheiroEvent", "DinheiroRemote", "DarDinheiro", "ReceberDinheiro", "RecompensaDinheiro",
    "MoedaEvent", "MoedaRemote", "DarMoedas", "AdicionarMoedas", "MoedasHandler",
    "AdicionarDinheiro", "ReceberMoedas", "DarCash", "AdicionarCash", "CashHandlerBR",
    -- RP: Empregos, salários, carteira, banco, pagamento
    "BankSystem", "BankService", "BankTransfer", "BankDeposit", "BankWithdraw", "PaySalary",
    "SalaryEvent", "SalaryRemote", "DailySalary", "PagarSalario", "ReceberSalario",
    "EmpregoPagar", "EmpregoReceber", "JobPay", "JobPayment", "JobReward", "JobEvent",
    "WorkReward", "WorkPay", "WorkEvent", "TrabalhoPagar", "TrabalhoReceber", "CarteiraEvent",
    "CarteiraRemote", "CarteiraVirtual", "CriarContaBancaria", "ContaBancariaRemote",
    "CartaoBancario", "CartaoRemote", "TransferirDinheiro", "TransferMoney", "ATMEvent",
    "ATMRemote", "PixEvent", "PixRemote", "ReceberPix", "EnviarPix", "ChequeEvent", "ChequeRemote",
    -- Extras que podem ser usados
    "DepositMoney", "WithdrawMoney", "GetMoney", "SetMoney", "BankBalance", "UpdateBalance",
    "AtualizarSaldo", "SaldoEvent", "SaldoRemote", "PagarConta", "PagarTaxa", "PagarMulta",
    "MultaEvent", "TaxEvent", "ReceberPremio", "PremioEvent", "PremioRemote"
}

-- Argumentos genéricos para todos os eventos (ajuste conforme necessário)
local args = {
    [1] = {
        ["\3"] = {
            [1] = {
                [1] = "Reward4"
            }
        }
    },
    [2] = {}
}

game:GetService("RunService").Stepped:Connect(function()
    for _, eventName in ipairs(possibleEvents) do
        local remote = game:GetService("ReplicatedStorage"):FindFirstChild(eventName)
        if remote and remote:IsA("RemoteEvent") then
            pcall(function()
                remote:FireServer(unpack(args))
            end)
        end
    end
end)
