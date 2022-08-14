# オープン・クローズド原則

## 概要

ソフトウェアの構成要素は拡張に対して開いていて、修正に対して閉じていなければならない

補足説明：既存の成果物を変更することなく、拡張することが可能であるように設計しなければならない

## 違反例
```php
/**
 * 
 * 会員クラス
 */
class Member
{
    /**
     * プラチナ会員
     */
    public const GRADE_PRATINUM = 'Platinum';

    /**
     * ゴールド会員
     */
    public const GRADE_GOLD = 'Gold';

    /**
     * シルバー会員
     */
    public const GLADE_SILVER = 'Silver';

    /**
     * 会員のグレード
     */
    public string $grade;

    /**
     * 支払い料金
     */ 
    public int $totalAmount;

    public function __construct(string $grade, int $totalAmount)
    {
        $this->grade = $grade
        $this->totalAmount = $totalAmount
    }
}

/**
 * 割引料金の計算を担うクラス
 */ 
class DiscountManager{
    
    /**
     * 割引額を適応した支払い料金を算出する
     * 
     * @return int
     */ 
    public function calculate(Member $member) : int
    {
        $discountAmount = 0;

        switch ($member->totalAmount)
        {
            case Member::GRADE_PRATINUM:
                $discountAmount = $member->totalAmount * 0.3
                break;

            case Member::GRADE_GOLD
                $discountAmount = $member->totalAmount * 0.2

            case Member::GRADE_SILVER:
                $discountAmount = $member->totalAmount * 0.1
                break;
        }

        return $member->totalAmount - $discountAmount;
    }
}
```
上記のコードの場合
会員グレードを足したい場合に、`DiscountManager`クラスの`calculate()`のswitch文に条件式を足すことになる
これが`既存の成果物を変更せず`に反している

## 解決策
```php
/**
 * Member interface
 */
interface MemberInterface
{
    /**
     * プラチナ会員
     */
    public const GRADE_PRATINUM = 'Platinum';

    /**
     * ゴールド会員
     */
    public const GRADE_GOLD = 'Gold';

    /**
     * シルバー会員
     */
    public const GRADE_SILVER = 'Silver';

    /**
     * ブロンズ会員
     */ 
    public const GRADE_BLONZE = 'Bronze';

    /**
     * 割引料金を算出する
     * 
     * @return float
     */
    public function calculate() : float;

    /**
     * 支払い料金の総額を取得する
     * 
     * @return float
     */
    public function getTotalAmount() : float;
}

/**
 * Platinum member
 */
class PlatinumMember implements MemberInterface
{
    /**
     * 割引率
     */ 
    private float $discount = 0.3;

    /**
     * 会員グレード
     */ 
    private string $grade = MemberInterface::GRADE_PRATINUM;

    /**
     * 支払い料金
     */
    private float $totalAmount;

    public function __construct(float $totalAmount) {
        $this->totalAmount = $totalAmount;
    }

    /**
     * 割引料金を算出する
     * 
     * @return float
     */
    public function calculate() : float
    {
        return $this->totalAmount * $this->discount;
    }

    /**
     * 支払い総額を取得する
     * 
     * @return float
     */
    public function getTotalAmount() : float
    {
        return $this->totalAmount;
    }
}

/**
 * Gold member
 */
class GoldMember implements MemberInterface
{
    /**
     * 割引率
     */ 
    private float $discount = 0.2;

    /**
     * 会員グレード
     */ 
    private string $grade = MemberInterface::GRADE_GOLD;

    /**
     * 支払い料金
     */
    public float $totalAmount;

    public function __construct(float $totalAmount) {
        $this->totalAmount = $totalAmount;
    }

    /**
     * 割引料金を算出する
     * 
     * @return float
     */
    public function calculate() : float
    {
        return $this->totalAmount * $this->discount;
    }

    /**
     * 支払い総額を取得する
     * 
     * @return float
     */
    public function getTotalAmount() : float
    {
        return $this->totalAmount;
    }
}

/**
 * Silver member
 */
class SilverMember implements MemberInterface
{
    /**
     * 割引率
     */ 
    private float $discount = 0.1;

    /**
     * 会員グレード
     */ 
    private string $grade = MemberInterface::GRADE_SILVER;

    /**
     * 支払い料金
     */
    public float $totalAmount;

    public function __construct(float $totalAmount) {
        $this->totalAmount = $totalAmount;
    }

    /**
     * 割引料金を算出する
     * 
     * @return float
     */
    public function calculate() : float
    {
        return $this->totalAmount * $this->discount;
    }

    /**
     * 支払い総額を取得する
     * 
     * @return float
     */
    public function getTotalAmount() : float
    {
        return $this->totalAmount;
    }

}

/**
 * Bronze member
 */
class BronzeMember implements MemberInterface
{
    /**
     * 割引率
     */ 
    private float $discount = 0.05;

    /**
     * 会員グレード
     */ 
    private string $grade = MemberInterface::GRADE_BLONZE;

    /**
     * 支払い料金
     */
    public float $totalAmount;

    public function __construct(float $totalAmount) {
        $this->totalAmount = $totalAmount;
    }

    /**
     * 割引料金を算出する
     * 
     * @return float
     */
    public function calculate() : float
    {
        return $this->totalAmount * $this->discount;
    }

    /**
     * 支払い総額を取得する
     * 
     * @return float
     */
    public function getTotalAmount() : float
    {
        return $this->totalAmount;
    }
}

/**
 * Discount manager
 */
class DiscountManager
{
    /**
     * 割引率を適応した支払い料金を算出する
     */
    public function calculate(MemberInterface $member) : float
    {
        $totalAmount = $member->getTotalAmount();
        return $totalAmount - $member->calculate();
    }
}
```
`MemberInterface`を定義しそれを実装した`PlatinumMember`, `GoldMember`, `SilverMember`
クラスをそれぞれ作成する
`DiscountManager`クラスからswitch文が消え、`calculate()`を呼ぶだけになる

新しくクレードを追加するときはその分クラスを追加すれば良いので、`既存の成果物`を変更することなく拡張できる。
