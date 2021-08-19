### Unity iOS Leaderboard 🏆
Simple script to have a leaderboard in your iOS Unity app.

### Setup
Get your leaderboard ID from your App Store Connect profile for your app. 

![lbid](img.png)

### Code
```csharp

using UnityEngine;

#if UNITY_IPHONE
using UnityEngine.iOS;
#endif

public class iOSLeaderboard : MonoBehaviour
{
    int score;
    int highScore;
    bool isHighHit;

    bool loginSuccessful;
    string leaderboardID = "YOUR LEADER BOARD ID";


    void Start()
    {
        highScore = PlayerPrefs.GetInt("Highscore");

#if UNITY_IPHONE

        // Sign user in to Gamecenter 
        AuthenticateUser();

        // Just incase didnt post first time 
        PostScoreOnLeaderBoard(highScore);
#endif
    }

    // Call to show your leader board 
    public void ShowLeaderBoard()
    {
        Social.ShowLeaderboardUI();
    }

    public void AddScore()
    {
        score += 1;

        // set your highscore
        if (score > highScore)
        {
            PlayerPrefs.SetInt("Highscore", score);
            isHighHit = true;
        }
    }

    // Call when your game has ended 
    public void EndGame()
    {
        // post your highscore if highscore has been acheived
        if (isHighHit)
        {
            PostScoreOnLeaderBoard(highScore);
        }
    }

    public void AuthenticateUser()
    {
        Social.localUser.Authenticate((bool success) =>
        {
            if (success)
            {
                loginSuccessful = true;
                // success
            }
            else
            {
                // no sucess
            }

        });

    }
    public void PostScoreOnLeaderBoard(int myScore)
    {
       
        if (loginSuccessful)
        {
            Social.ReportScore(myScore, leaderboardID, (bool success) =>
            {

                if (success)
                    Debug.Log("uploaded");
            });
        }
        else

        {

            Social.localUser.Authenticate((bool success) =>
            {

                if (success)

                {

                    loginSuccessful = true;

                    Social.ReportScore(myScore, leaderboardID, (bool successful) =>
                    {

                        // handle after 
                    });

                }
                else
                {
                    // unsuccessful
                }
               
            });
        }
       
    }

}


```
