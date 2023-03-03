# Reservoir Sampling

```c++
int n = 0;  // Running count of elements observed so far  
#define SIZE 10000
int reservoir[SIZE];  

while(streamHasData())
{
  int x = readNumberFromStream();

  if (n < SIZE)
  {
       reservoir[n++] = x;
  }         
  else 
  {
      int p = random(++n); // Choose a random number 0 >= p < n
      if (p < SIZE)
      {
           reservoir[p] = x;
      }
  }
}
```