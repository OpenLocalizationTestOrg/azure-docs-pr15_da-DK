> [AZURE.NOTE]
> Filen .publishsettings indeholder dine legitimationsoplysninger (ikke-kodede spidsbåndbredde), der bruges til at administrere dine Azure abonnementer og tjenester. Af sikkerhedsmæssige årsager til denne fil er at gemme den midlertidigt uden for din datakilde kataloger (for eksempel i mappen Libraries\Documents), og derefter slette den, når importen er færdig. En skadelig bruger, der får adgang til filen .publishsettings kan redigere, oprette og slette dine Azure tjenester.