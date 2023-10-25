# release-v6.9.0-rc.0-genesis-6

## Genesis Instructions

### FIRST -> CLEANUP!
1. **Delete Previous Data**: Ensure you have deleted any previously forked `release-v6.9.0-rc.0-genesis-2` from your GitHub organization.
    ```bash
    rm -Rf ~/libra-framework
    rm -Rf ~/.libra/data && rm -Rf ~/.libra/genesis && rm -Rf ~/.libra/secure-data.json
    ```

2. **Retrieve Validator Address**: You can retrieve the validator address using the following command:
    ```bash
    grep 'account_address' ~/.libra/public-keys.yaml
    ```

3. **Fetch Source & Verify Commit Hash**:
    ```bash
    cd ~
    git clone https://github.com/0LNetworkCommunity/libra-framework
    cd ~/libra-framework
    git fetch --all && git checkout main
    git log -n 1 --pretty=format:"%H"
    ```

4. **Build libra-framework Packages**:
    ```bash
    cd ~/libra-framework
    cargo build --release -p libra -p libra-genesis-tools -p libra-txs -p diem-db-tool
    ```

5. **Prepare .libra Directory & Add Github Key**:
    ```bash
    mkdir ~/.libra/
    nano ~/.libra/github_token.txt
    ```

6. **Pre-Genesis Registration**:
    - Ensure you delete any forked version of `release-v6.9.0-rc.0-genesis-2` in your home organization before registering.
    ```bash
    cd ~/libra-framework
    ./target/release/libra-genesis-tools register  --org-github 0LNetworkCommunity --name-github release-v6.9.0-rc.0-genesis-6
    ```

7. **Coordinator Steps**:
    - PR Received
    - PR Merged
    - All nodes added to `layout.yaml` users key. (Wait for coordinator)

8. **Build JSON_Legacy**:
    - (Skip this step)
    ```bash
    git clone https://github.com/0L-Analytics/ol-data-extraction
    cd ol-data-extraction
    mkdir assets
    cp services/treeview/example.env services/treeview/.env
    sed -i 's/<insert_password>/869cf751355b4f4b397606f2b3e398c6/g' services/treeview/.env
    docker compose up -d treeview
    ```

9. **Build JSON_Legacy (Alternative Method)**:
    ```bash
    sudo rm -Rf ~/ol-data-extraction
    docker rmi --force $(docker images | grep treeview | awk '{print $3}')
    cd ~ && git clone -b v-6.9.x-ready https://github.com/sirouk/ol-data-extraction
    cd ~/ol-data-extraction
    docker compose up -d treeview && docker logs --follow treeview
    md5sum ~/ol-data-extraction/assets/data.json
    ```

10. **Pull from Genesis Repo and Build**:
    ```bash
    cd ~/libra-framework/tools/genesis
    GIT_ORG=0LNetworkCommunity GIT_REPO=release-v6.9.0-rc.0-genesis-6 RECOVERY_FILE=~/v5.2_recovery.json make genesis
    ```

11. **Start Nodes!**:
    ```bash
    ~/libra-framework/target/release/libra node --config-path ~/.libra/validator.yaml
    ```
