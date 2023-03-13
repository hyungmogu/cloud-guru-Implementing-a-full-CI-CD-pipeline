# Building an App as a Freestyle Jenkins Project

## Setting Up Jenkins Project

1. Click `New Item`

2. Name project, and select `Freestyle Project`


<img src="https://user-images.githubusercontent.com/6856382/224584203-f090549f-5681-41d3-b6ef-baf171727c26.png">

3. click `git` under `source code management`

4. add `Repository URL` from github (should be under `clone` tab)
- leave `credentials` alone for now

5. add build step by choosing `invoke grade script`

6. define entrypoint for gradle wrapper

7. set destination to fetch artifacts once build is done

<img src="https://user-images.githubusercontent.com/6856382/224583757-57f2ec0c-8d26-4c0a-8b2a-92737ed05744.png">

#