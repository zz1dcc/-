using UnityEngine;

public class GameController : MonoBehaviour
{
    public Player player;
    public Partner[] partners;
    public Enemy[] enemies;
    public Level[] levels;
    public AudioClip levelMusic;

    private int currentLevelIndex = 0;

    void Start()
    {
        StartLevel(currentLevelIndex);
    }

    void StartLevel(int levelIndex)
    {
        Level currentLevel = levels[levelIndex];
        AudioSource.PlayClipAtPoint(currentLevel.levelMusic, transform.position);
        foreach (var enemy in currentLevel.enemies)
        {
            Instantiate(enemy, transform.position, Quaternion.identity);
        }
    }

    void Update()
    {
        // 每帧更新玩家和伙伴的状态
        player.UpdatePlayerState();
        foreach (var partner in partners)
        {
            partner.UpdatePartnerState();
        }

        // 检查敌人攻击
        foreach (var enemy in enemies)
        {
            if (enemy.isBoss)
            {
                enemy.AttackPlayer(player);
            }
            else
            {
                enemy.AttackPlayer(player);
            }
        }
    }
}

public class Player : MonoBehaviour
{
    public int health;
    public int attackPower;
    public bool canMove = true;

    public void UpdatePlayerState()
    {
        // 更新玩家的状态，比如移动、攻击等
    }

    public void TakeDamage(int damage)
    {
        health -= damage;
    }

    public void Heal(int amount)
    {
        health += amount;
    }

    public void DisableAttack(float time)
    {
        // 暂时禁用攻击
    }
    
    public void FreezeEnemies(float time)
    {
        // 冻结敌人
    }
}

public class Partner : MonoBehaviour
{
    public string partnerName;
    public int specialSkillCooldown;
    private bool canUseSkill = true;

    public void UseSpecialSkill(Player player)
    {
        if (canUseSkill)
        {
            if (partnerName == "罗毅")
            {
                // 罗毅的“军训”技能
                player.FreezeEnemies(2f);
                canUseSkill = false;
                Invoke("ResetSkillCooldown", specialSkillCooldown);
            }
        }
    }

    private void ResetSkillCooldown()
    {
        canUseSkill = true;
    }

    public void UpdatePartnerState()
    {
        // 更新伙伴状态，比如使用技能等
    }
}

public class Enemy : MonoBehaviour
{
    public string enemyName;
    public int health;
    public int attackPower;
    public bool isBoss;
    public AudioClip attackSound;

    public void AttackPlayer(Player player)
    {
        if (isBoss)
        {
            if (enemyName == "王大宝")
            {
                // 无处可逃的技能
                player.FreezeEnemies(2f);
                AudioSource.PlayClipAtPoint(attackSound, transform.position);
            }
            else if (enemyName == "张军辉")
            {
                // 张军辉的侮辱攻击
                player.TakeDamage(attackPower);
                player.ApplyStatusEffect("侮辱", 5f);
            }
        }
        else
        {
            // 普通敌人攻击
            player.TakeDamage(attackPower);
        }
    }
}

public class Level : MonoBehaviour
{
    public string levelName;
    public AudioClip levelMusic;
    public GameObject[] enemies;
    public GameObject[] items;

    public void StartLevel()
    {
        AudioSource.PlayClipAtPoint(levelMusic, transform.position);
        foreach (var enemy in enemies)
        {
            Instantiate(enemy, transform.position, Quaternion.identity);
        }
        foreach (var item in items)
        {
            Instantiate(item, transform.position, Quaternion.identity);
        }
    }
}

public class Item : MonoBehaviour
{
    public enum ItemType { Food, Attack }
    public ItemType itemType;
    public int healAmount;
    public int attackBoost;

    public void UseItem(Player player)
    {
        if (itemType == ItemType.Food)
        {
            player.Heal(healAmount);
        }
        else if (itemType == ItemType.Attack)
        {
            player.attackPower += attackBoost;
        }
        Destroy(gameObject);
    }
}

public class SecretEvent : MonoBehaviour
{
    public string secretEventName;
    public AudioClip secretSound;

    public void TriggerSecretEvent(Player player)
    {
        if (secretEventName == "贺子睿遭遇张军辉")
        {
            player.DisableAttack(5f);
            AudioSource.PlayClipAtPoint(secretSound, transform.position);
        }
    }
}

public class FinalBoss : MonoBehaviour
{
    public int health;
    public int attackPower;
    private bool isPhaseTwo = false;

    public void StartBossBattle(Player player)
    {
        if (health <= 0)
        {
            Debug.Log("蔡英杰被击败！");
            // 游戏胜利处理
        }
        else
        {
            if (!isPhaseTwo && health <= 50)
            {
                isPhaseTwo = true;
                ChangeBossPhase();
            }
            else
            {
                player.TakeDamage(attackPower);
            }
        }
    }

    private void ChangeBossPhase()
    {
        Debug.Log("蔡英杰进入第二阶段！");
    }
}
