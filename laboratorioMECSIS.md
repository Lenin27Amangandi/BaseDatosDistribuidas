```sql
WITH MateriasComunes AS
(
    SELECT DISTINCT mMEC.idmat
    FROM dbo.matriculaMEC AS mMEC
    WHERE EXISTS
    (
        SELECT 1
        FROM [WIN-HLAF1T6LIRO].[SIS].[dbo].[matriculaSIS] AS mSIS
        WHERE mSIS.idmat = mMEC.idmat
    )
)

SELECT
    eMEC.nombre,
    eMEC.apellido,
    mMEC.idmat,
    'MEC' AS origen
FROM dbo.matriculaMEC AS mMEC
INNER JOIN dbo.estudianteMEC AS eMEC
    ON eMEC.CC = mMEC.CC
INNER JOIN MateriasComunes AS mc
    ON mc.idmat = mMEC.idmat

UNION ALL

SELECT
    eSIS.nombre,
    eSIS.apellido,
    mSIS.idmat,
    'SIS' AS origen
FROM [WIN-HLAF1T6LIRO].[SIS].[dbo].[matriculaSIS] AS mSIS
INNER JOIN [WIN-HLAF1T6LIRO].[SIS].[dbo].[estudianteSIS] AS eSIS
    ON eSIS.CC = mSIS.CC
INNER JOIN MateriasComunes AS mc
    ON mc.idmat = mSIS.idmat;
```
