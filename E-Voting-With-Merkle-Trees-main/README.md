# Secure E-Voting System

Secure E-Voting System is a python based project, whose main focus is handling the security of data before, during and after elections takes place. It also allows auditing of votes to ensure an unbiased voting environment.

The encryption method used to ensure safety are:-

    RSA Encryption 
    Merkle Tree Hashing
  
The Libraries used are:-

    hashlib
    pickle
    random,
    csv
    flask 
    wtforms

# registeration.py
This file focuses on the registeration procedure of the voter. It takes as input a list of all data that is collected from the form and the collected data is then stores in Files/voters_data.csv. Here the candidates voter ID is created and shown to them on the form so that they can save it with themselves for whenever they want to vote. At the backend a Public and Private key of the voter is created and stored in order for encryption and decryption to take place in the other parts of the system. The method used tp generate the Public and Privates keys and the encryption and decryption of vote in further files is followed from this article:-

    https://core.ac.uk/download/pdf/11779635.pdf

# vote_cast.py
This is the file where the vote is casted. The  voter's name, voter ID and the vote for their candidate is given to the function run_polls() in this file as a list. Using the voter ID the private key is retreived from the file Files/voters_data.csv. Once retreived a ballot is created for this voter and their ballot ID and voter ID is stored in Files/ballots.csv, once stored the vote of the voter is encrypted with the extracted private key using the RSA encryption method and then this encrypted vote and the ballot ID that was generated previously is stored in Files/polls.csv. 

# store_votes.py
It reads data from all the files used for mapping and storing generated data. It uses the ballot.json to verify the ballot ID against the ballot ID present in polls.json file. It is also used to figure out the corresponding voter ID to any given ballot ID. Using the voter ID, we access the voter's public key to decrypt the encrypted vote. The vote comprises of a candidate ID. The sequence of candidate IDs is shown as a result. The sequence follows the same structure as the votes were stored. This sequence of candidate IDs is then hashed using SHA-256 and used as a leaf node to form a merkle tree. This tree is then dumped using pickle, in a file called Merkle.

# countserv.py
This file reads data from the Merkle file and counts the total votes for each candidate by using a counter for each of them while reading it from the Merkle file. The file initially reads from the pickled 'merkle' file which consists of the MerkleTools object. This object has the whole merkle tree in an immutable, binary form to read and process the leaves. The leaves of the Merkle Tree consists of the individual vote received for the candidate. The merkle tree helps in protecting the vote data from any kind of tampering and preserves the state of the voting sequence.


# auditserv.py
This file is used to verify the state of the merkle tree which is pickled from the 'Merkle' file. In this, a new candidate is taken from the argument in the command line. It is then checked against the Merkle file to check if it is a valid leaf node or sister node. If not, it will have a different root than the actual root. This way we can verify if the votes have ever been tampered at any time. Validating the proof of the target node provided in the argument makes the audit a success. If the node exists as leaf, then the tree is valid. Whereas it gives a false response on reading the tampered tree thereby verifying the voting sequence.