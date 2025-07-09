for _, v in pairs(game:GetDescendants()) do
    if v:IsA("RemoteEvent") or v:IsA("RemoteFunction") then
        print("[🔍 REMOTO ENCONTRADO] >>", v:GetFullName(), " - ", v.ClassName)
    end
end
