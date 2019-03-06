This file mpi hello-world and example how to work its ;
#include "mpi.h"
#include <stdio.h>
int main( int argc, char *argv[] )
{
    int rank, size;
    MPI_Init( &argc, &argv );
    MPI_Comm_rank( MPI_COMM_WORLD, &rank );
    MPI_Comm_size( MPI_COMM_WORLD, &size );
    printf( "Hello World from process %d of %d\n", rank, size );
    MPI_Finalize();
    return 0;
}
*/
/*
#include <mpi.h>
#include <stdio.h>
#include <math.h>
#include <conio.h>
#include <iostream>
#include <stdlib.h>
int main( int argc, char *argv[] )
{
MPI_Init( &argc, &argv );
const int root=0;
int number,a,b;
int world_rank,world_size;;
MPI_Comm_rank(MPI_COMM_WORLD, &world_rank);
MPI_Comm_size(MPI_COMM_WORLD, &world_size);


if (world_rank == 0)
{
	std::cout<<"a-->"<<"\t"<<"b-->"<<"\n";
    std::cin>>a>>b;
    number=a+b;
	MPI_Send(&number, 1, MPI_INT, 1, 0, MPI_COMM_WORLD);
}
else if (world_rank == 1) 
{
    MPI_Recv(&number, 1, MPI_INT, 0, 0, MPI_COMM_WORLD,MPI_STATUS_IGNORE);
    std::cout<<"Process 1 received number %d from process 0  \t"<<number<<"\n";
}
*/
  /* 
	if (world_rank == 0)
	{
	std::cout<<"a-->"<<"\t"<<"b-->"<<"\n";
    std::cin>>a>>b;
    number=a+b;
	}
	 MPI_Bcast(&number,1,MPI_INT,0,MPI_COMM_WORLD);
     std::cout<< "Process "<<world_rank<<" received number %d from process 0 \t "<<number;
	
	MPI_Finalize();
	return 0;
}
*/
/*
#include <stdio.h>
#include <mpi.h>
int main (int argc, char* argv[])
{
  int errCode;

  if ((errCode = MPI_Init(&argc, &argv)) != 0)
  {
    return errCode;
  }

  int myRank,size;
  MPI_Comm_size(MPI_COMM_WORLD, &size);
  MPI_Comm_rank(MPI_COMM_WORLD, &myRank);

  //if (myRank == 1)
  {
    printf("It works!%d of %d\n",myRank,size);
  }

  MPI_Finalize();
  return 0;
}
*/

#include "mpi.h"
#include <stdio.h>
void addem ( int *, int *, int *, MPI_Datatype * ); 
void addem(int *invec, int *inoutvec, int *len, MPI_Datatype *dtype)
{
    int i;
    for ( i=0; i<*len; i++ ) 
        inoutvec[i] += invec[i];
}
 
int main( int argc, char **argv )
{
    int rank, size, i;
    int data;
    int errors=0;
    int result = -100;
    int correct_result;
    MPI_Op op;
 
    MPI_Init( &argc, &argv );
    MPI_Comm_rank( MPI_COMM_WORLD, &rank );
    MPI_Comm_size( MPI_COMM_WORLD, &size );
 
    data = rank;
    MPI_Op_create( (MPI_User_function *)addem, 1, &op );
    MPI_Reduce ( &data, &result, 1, MPI_INT, op, 0, MPI_COMM_WORLD );
    MPI_Bcast ( &result, 1, MPI_INT, 0, MPI_COMM_WORLD );
   // MPI_Op_free( &op );
    correct_result = 0;
    for(i=0;i<size;i++) 
        correct_result += i;
    if (result != correct_result) errors++;
 
    MPI_Finalize();
    return errors;
}
