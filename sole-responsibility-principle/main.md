# 単一責任の原則 (SRP)

## 概要
モジュールは単一のアクターに対して責任を負うべきである

- モジュールとは
いくつかの関数やデータをまとめた凝集性のあるもの
- アクターとは
実際にモジュールやそのモジュールに含まれる関数を実行するもの、以下の例だと
    - 経理部門
    - 人事部門
    - 技術部門

    などがアクターに該当する

## 違反例
```php
/**
 * 
 * 従業員クラス
 * 
 */
class Employee
{
    /**
     * 給与の計算を行うメソッド
     * 経理部門に対して責任を負っている
     */
    public function calculatePay() : Money
    {
        // getRegularHoursから業務時間を取得し
        $regularHours = $this->getRegularHours();
        // 給与を計算する
    }

    /**
     * 労働時間をレポート出力するメソッド
     * 人事部門に対して責任を負っている
     */
    public function reportHours() : string
    {
        // getRegularHoursから業務時間を取得し
        $regularHours = $this->getRegularHours();
        // レポートを出力する
    }

    /**
     * 従業員データをDBに保存する
     * 技術部門に対して責任を負っている
     */ 
    public function save()
    {
        // 従業員をDBに保存する
    }

    /**
     * 従業員の業務時間を算出し返却する
     */
    private function getRegularHours() : int
    {
        // 業務時間を返却する
    }
}
```
開発時、`calculatePay()`と`reportHours()`での業務時間の算出方法が同一であったため、業務時間の算出をメソッドとして切り出した
リリース後、`reportHours()`での業務時間の算出方法を変更したいとの要望が出た場合に
`getRegularHours()`を修正してしまうと`calculatePay()`での業務時間の算出にも影響が出てしまう

`Employee`クラス（モジュール）は経理部門、人事部門、技術部門の3つのアクターに対して責任を負ってしまっているため、単一責任の原則に違反していることとなる。

## 解決策
```php
/**
 * 従業員のデータプロパティのみ所有するクラス
 */
class EmployeeData
{
    public string $name;
    public string $dutyHours;

    public function __construct(string $name, string $dutyHours)
    {
        $this->name = $name;
        $this->dutyHours = $dutyHours;
    }
}

/**
 * 給与計算を行うクラス
 */
class PayCalculator
{
    public EmployeeDate $employData;

    public function __constructor(EmployeeData $employeeData)
    {
        $this->employeeData = $employeeData
    }

    /**
     * 労働時間をレポート出力するメソッド
     * 人事部門に対して責任を負っている
     */
    public function reportHours() : string
    {
        // getRegularHoursから業務時間を取得し
        $regularHours = $this->getRegularHours();
        // レポートを出力する
    }

    /**
     * 従業員の業務時間を算出し返却する
     */
    private function getRegularHours() : int
    {
        // 業務時間を返却する
    }
}


/**
 * 業務時間のレポート出力を担うクラス
 */
class HourReporter
{
    public EmployeeDate $employData;

    public function __constructor(EmployeeData $employeeData)
    {
        $this->employeeData = $employeeData
    }

    /**
     * 労働時間をレポート出力するメソッド
     * 人事部門に対して責任を負っている
     */
    public function reportHours() : string
    {
        // getRegularHoursから業務時間を取得し
        $regularHours = $this->getRegularHours();
        // レポートを出力する
    }

    /**
     * 従業員の業務時間を算出し返却する
     */
    private function getRegularHours() : int
    {
        // 業務時間を返却する
    }
}


/**
 * 従業員データの保存を担うクラス
 */ 
class EmployeeServer
{
    public EmployeeDate $employData;

    public function __constructor(EmployeeData $employeeData)
    {
        $this->employeeData = $employeeData
    }

    /**
     * 従業員データをDBに保存する
     */ 
    public function save()
    {
        // 従業員をDBに保存する
    }
}
```

給与計算、業務時間のレポート出力、従業員データの保存を別々のクラスに分けることによって
- 経理部門 -> `PayCalculator`クラス
- 人事部門 -> `HourReporter`クラス
- 技術部門 -> `EmployeeServer`クラス

といったようにそれぞれのクラス（モジュール）が、単一のアクターにのみ責任を負う形となり、単一責任の原則に則る形となっている。